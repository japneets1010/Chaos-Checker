# Chaos Checker - Team Setup Guide

**Welcome!** This guide will get you up and running with Chaos Checker using Autodesk's AMP (AI/ML Platform) in under 15 minutes.

## 📋 Prerequisites Checklist

Before you start, ensure you have:

- [ ] Access to Autodesk VPN (required)
- [ ] macOS or Linux (Windows users: use WSL2)
- [ ] Python 3.13 installed (`python3.13 --version`)
- [ ] Node.js 18+ installed (`node --version`)
- [ ] Git configured with Autodesk credentials
- [ ] Terminal/command line access

**Note**: AMP CLI (`amp` command) is needed but will be checked/installed during setup.

**Don't have Python 3.13?**
```bash
# macOS (using Homebrew)
brew install python@3.13

# Ubuntu/Debian
sudo apt install python3.13

# Verify
python3.13 --version  # Should show: Python 3.13.x
```

---

## 🚀 Setup Steps

### Step 1: Clone the Repository

```bash
# Clone the repo
git clone <REPO_URL>
cd chaos-checker

# Verify you're in the right place
ls -la  # You should see: backend/, frontend/, README.md, etc.
```

---

### Step 2: Create APS Application

**What is APS?** Autodesk Platform Services - provides authentication for AMP.

1. **Go to APS Portal**: https://aps-dev.autodesk.com/
   - Sign in with your Autodesk credentials
   - Connect to VPN if prompted

2. **Create New Application**:
   - Click **"Create Application"**
   - Application Type: **"Server-to-Server"** (2-legged OAuth)
   - Name: `chaos-checker-<YOUR_NAME>` (e.g., `chaos-checker-john`)
   - Description: `Formal verification tool for system designs`
   - Click **"Create"**

3. **Save Your Credentials**:
   ```
   Client ID: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
   Client Secret: yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy
   ```

   ⚠️ **IMPORTANT**: Copy the Client Secret NOW - it's only shown once!

   📝 **Store them securely** (password manager, secure notes, etc.)

---

### Step 3: Check if AMP CLI is Available

The AMP CLI lets you authorize your application to use AI models.

**First, check if you already have it:**

```bash
# Check if amp CLI is installed
amp --help
```

**If you see the AMP CLI help text**, you're all set! Skip to Step 4.

**If you get "command not found"**, you need to install it:

<details>
<summary>📦 Click here for AMP CLI installation instructions</summary>

**Method 1: Install from Git** (Recommended - simplest!)

```bash
# Install AMP CLI from Autodesk's internal Git repository
pip3 install --upgrade git+ssh://git@git.autodesk.com/mlops-platform/amp-core-python-hub.git#subdirectory=amp

# Verify installation
amp --help
```

**Requirements**:
- SSH access to Autodesk's Git repositories
- Your Git credentials should already be configured

**If you get SSH errors**, make sure your SSH keys are set up for Autodesk Git.

---

**Method 2: Install from Artifactory** (Alternative)

If the Git method doesn't work, use Artifactory:

```bash
# Get your Artifactory token from: https://art-bobcat.autodesk.com/ui/user_profile

# Install (replace YOUR_EMAIL and YOUR_TOKEN)
pip3 install --upgrade adsk-amp \
  --index-url https://YOUR_EMAIL:YOUR_TOKEN@art-bobcat.autodesk.com/artifactory/api/pypi/ad-amp-pypi/simple \
  --extra-index-url https://pypi.org/simple

# Verify
amp --help
```

---

### Step 4: Authorize Your Application for AI Models

Your APS application needs permission to use specific AI models.

```bash
# Authorize for Claude 4 Sonnet (recommended model)
amp deployments authorize \
  --deployment-name=playground-claude-4-sonnet \
  --client-id=YOUR_CLIENT_ID \
  --env=dev

# You should see:
# ✅ Client-id validated successfully in dev environment
# 🔓 Your APS client id has been successfully authorized.
# ⏱️ Please wait up to 2 min for the changes to be applied.
```

**Wait 2 minutes** before proceeding - authorization takes time to propagate.

**Optional**: Authorize additional models for testing:
```bash
# DeepSeek (reasoning model)
amp deployments authorize \
  --deployment-name=playground-deepseek-r1-1 \
  --client-id=YOUR_CLIENT_ID \
  --env=dev

# GPT-4o (OpenAI model)
amp deployments authorize \
  --deployment-name=playground-gpt-4o \
  --client-id=YOUR_CLIENT_ID \
  --env=dev
```

---

### Step 5: Configure Backend

```bash
cd backend

# Create .env file from template
cp env.template .env

# Edit .env file with your credentials
```
**Use your favorite editor: nano, vim, or VSCode**

**Update these values in `.env`**:
```bash
APS_CLIENT_ID=YOUR_ACTUAL_CLIENT_ID_FROM_STEP_2
APS_CLIENT_SECRET=YOUR_ACTUAL_CLIENT_SECRET_FROM_STEP_2
AMP_ENV=dev
AMP_DEPLOYMENT=playground-claude-4-sonnet
LLM_PROVIDER=amp
HOST=0.0.0.0
PORT=8000
```

---

### Step 6: Setup Backend Environment

```bash
# Make sure you're in the backend directory
cd backend  # If not already there

# Create virtual environment with Python 3.13
python3.13 -m venv venv

# Activate virtual environment
source venv/bin/activate
# Your prompt should now show: (venv)

# Install dependencies
pip install --upgrade pip
pip install -r requirements.txt

# This will take 1-2 minutes
# You should see: "Successfully installed fastapi uvicorn pydantic..."
```

---

### Step 7: Test Backend

```bash
# Make sure you're in backend/ with venv activated
# You should see (venv) in your prompt

# Start the backend
python app.py
```

**Expected output**:
```
INFO:services.llm_service:Initialized AMP LLM Service: env=dev, deployment=playground-claude-4-sonnet, type=bedrock
INFO:     Will watch for changes in these directories: ['/path/to/backend']
INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
INFO:     Started reloader process [12345] using StatReload
INFO:     Started server process [12346]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
```

**✅ Success indicators**:
- You see "Initialized AMP LLM Service"
- Model type shows "bedrock"
- Server is "running on http://0.0.0.0:8000"
- No errors appear

**Keep this terminal running** - don't close it!

---

### Step 8: Test API (New Terminal)

Open a **new terminal window/tab** and run:

```bash
# Test health endpoint
curl http://localhost:8000/

# Expected response:
# {"status":"healthy","service":"Chaos Checker API","version":"1.0.0"}
```

**✅ If you see this**, your backend is working!

**Test actual AI generation** (this will take 5-15 seconds):

```bash
curl -X POST http://localhost:8000/api/validate \
  -H "Content-Type: application/json" \
  -d '{
    "diagram_xml": "<?xml version=\"1.0\" encoding=\"UTF-8\"?><mxfile><diagram><mxGraphModel><root><mxCell id=\"0\"/><mxCell id=\"1\" parent=\"0\"/><mxCell id=\"2\" value=\"idle\" style=\"ellipse\" vertex=\"1\" parent=\"1\"><mxGeometry x=\"120\" y=\"120\" width=\"80\" height=\"80\" as=\"geometry\"/></mxCell><mxCell id=\"3\" value=\"running\" style=\"ellipse\" vertex=\"1\" parent=\"1\"><mxGeometry x=\"280\" y=\"120\" width=\"80\" height=\"80\" as=\"geometry\"/></mxCell><mxCell id=\"4\" value=\"start\" edge=\"1\" parent=\"1\" source=\"2\" target=\"3\"><mxGeometry relative=\"1\" as=\"geometry\"/></mxCell></root></mxGraphModel></diagram></mxfile>",
    "specification_language": "tlaplus"
  }' | python3 -m json.tool
```

**Expected output**:
```json
{
  "success": true,
  "generated_spec": "EXTENDS Naturals\n\nVARIABLE state\n\nInit == state = \"idle\"...",
  "validation_result": {
    "status": "success"
  },
  "message": "Design is valid! No issues found."
}
```

**✅ If you see `"success": true` and a TLA+ spec**, you're fully set up!

---

### Step 9: Setup Frontend

In a **new terminal window/tab**:

```bash
cd frontend

# Install dependencies
npm install
# This takes 2-3 minutes

# Start development server
npm run dev
```

**Expected output**:
```
> frontend@0.1.0 dev
> next dev

   ▲ Next.js 14.x.x
   - Local:        http://localhost:3000
   - Network:      http://192.168.x.x:3000

 ✓ Ready in 2.5s
```

**Open your browser**: http://localhost:3000

**✅ You should see** the Chaos Checker interface!

---

### Step 10: Verify Everything Works

1. **In the web interface**:
   - Draw a simple diagram using the draw.io editor
   - Click "Validate Design"
   - You should see TLA+ code generated in 5-15 seconds

2. **Check both terminals are running**:
   - Terminal 1: Backend (port 8000) ✓
   - Terminal 2: Frontend (port 3000) ✓

3. **Test model switching** (optional):
   - Stop backend (Ctrl+C in backend terminal)
   - Edit `backend/.env`: Change `AMP_DEPLOYMENT=playground-deepseek-r1-1`
   - Restart: `python app.py`
   - Test again - should work with different model

---

## 🎯 Quick Reference

### Daily Usage

**Start Backend**:
```bash
cd backend
source venv/bin/activate
python app.py
```

**Start Frontend**:
```bash
cd frontend
npm run dev
```

**Stop Servers**:
- Press `Ctrl+C` in each terminal

### Available Models

List all models:
```bash
amp deployments list --env=dev --project-id=AMP-PID-1403
```

**Recommended for formal verification**:
- ✅ `playground-claude-4-sonnet` (best quality)
- `playground-claude-3-7-sonnet` (stable alternative)
- `playground-deepseek-r1-1` (reasoning, experimental)
- `playground-gpt-4o` (OpenAI, fallback)

**Switch models**:
1. Authorize if needed: `amp deployments authorize --deployment-name=<MODEL> --client-id=<YOUR_ID> --env=dev`
2. Edit `backend/.env`: Change `AMP_DEPLOYMENT=<MODEL>`
3. Restart backend

---

## 🐛 Common Issues & Solutions

### Issue: "ModuleNotFoundError: No module named 'fastapi'"

**Solution**: You forgot to activate the virtual environment
```bash
cd backend
source venv/bin/activate  # Your prompt should show (venv)
python app.py
```

---

### Issue: "APS authentication failed: 401"

**Causes & Solutions**:

1. **Wrong credentials in .env**
   ```bash
   # Check your .env file
   cat backend/.env | grep APS_CLIENT
   # Verify these match what you saved from Step 2
   ```

2. **Extra spaces in .env file**
   ```bash
   # Bad:  APS_CLIENT_ID= abc123  (spaces!)
   # Good: APS_CLIENT_ID=abc123   (no spaces!)
   ```

3. **Using wrong environment**
   - Make sure `AMP_ENV=dev` in `.env`
   - Make sure you created APS app in dev environment

---

### Issue: "AIS request failed: 403 Forbidden"

**Causes & Solutions**:

1. **Not authorized for this model**
   ```bash
   # Authorize the model
   amp deployments authorize \
     --deployment-name=playground-claude-4-sonnet \
     --client-id=YOUR_CLIENT_ID \
     --env=dev

   # Wait 2 minutes, then try again
   ```

2. **Authorization not propagated yet**
   - Wait 2-3 minutes after running `amp deployments authorize`
   - Then restart backend and try again

---

### Issue: "Port 8000 already in use"

**Solution**: Kill the existing process
```bash
# Find what's using port 8000
lsof -ti:8000

# Kill it
lsof -ti:8000 | xargs kill -9

# Try starting backend again
python app.py
```

---

### Issue: Backend starts but "No mxGraphModel found"

**Cause**: Diagram XML format issue (this is normal for invalid diagrams)

**Solution**: Use the test curl command from Step 8 to verify backend works. The error only occurs with malformed diagrams.

---

### Issue: "Python 3.14 not supported"

**Solution**: Use Python 3.13
```bash
cd backend
rm -rf venv  # Delete old venv
python3.13 -m venv venv  # Create with 3.13
source venv/bin/activate
pip install -r requirements.txt
```

---

### Issue: Frontend shows "Failed to fetch"

**Causes & Solutions**:

1. **Backend not running**
   - Start backend in another terminal: `cd backend && source venv/bin/activate && python app.py`

2. **Wrong API URL**
   ```bash
   # Check frontend/.env.local exists and has:
   NEXT_PUBLIC_API_URL=http://localhost:8000
   ```

3. **CORS issue**
   - This shouldn't happen with our config, but if it does:
   - Check `backend/app.py` has `allow_origins=["*"]`

---

### Issue: "amp: command not found"

**Solution 1**: Check if it's in your PATH
```bash
# Check if installed
which amp

# Check if installed via pip
pip3 list | grep amp
```

**Solution 2**: Install from Git (simplest method)
```bash
pip3 install --upgrade git+ssh://git@git.autodesk.com/mlops-platform/amp-core-python-hub.git#subdirectory=amp
```

**Solution 3**: Install from Artifactory (if Git doesn't work)
```bash
# Get token from: https://art-bobcat.autodesk.com/ui/user_profile
pip3 install --upgrade adsk-amp \
  --index-url https://YOUR_EMAIL:YOUR_TOKEN@art-bobcat.autodesk.com/artifactory/api/pypi/ad-amp-pypi/simple \
  --extra-index-url https://pypi.org/simple
```

**Solution 4**: Ask your team lead
- They may have specific installation instructions for your team

---

## 📚 Additional Resources

### Project Documentation
- **`README.md`** - Project overview
- **`INTEGRATION_COMPLETE.md`** - Detailed AMP integration info
- **`QUICKSTART_AMP.md`** - Quick reference for daily use

### Autodesk Resources
- **AMP Support Slack**: [#pset-ad-amp-support](https://autodesk.enterprise.slack.com/archives/C05F3UT42L9)
- **AMP Documentation**: https://beacon.autodesk.com/catalog/default/component/autodesk-machine-learning-platform/docs
- **APS Portal (dev)**: https://aps-dev.autodesk.com/
- **AMP Playground**: https://data.autodesk.com/amp

### Get Help
1. **Check this guide** - Most issues are covered above
2. **Ask your team** - Someone else probably hit the same issue
3. **Slack support**: [#pset-ad-amp-support](https://autodesk.enterprise.slack.com/archives/C05F3UT42L9)
4. **Email**: ian.sebanja@autodesk.com (AMP Product Manager)

---

## ✅ Setup Complete Checklist

Before you start coding, verify:

- [ ] Backend starts without errors
- [ ] Health check works: `curl http://localhost:8000/`
- [ ] Test validation works (curl command from Step 8)
- [ ] Frontend loads at http://localhost:3000
- [ ] You can draw diagrams and generate TLA+ code
- [ ] `.env` file is NOT in git (`git status` should not show it)

**All checked?** 🎉 **You're ready to go!**

---

## 🔐 Security Reminders

❌ **NEVER commit**:
- `backend/.env` file (contains secrets)
- Your APS Client Secret
- Any access tokens

✅ **These are safe to commit**:
- `backend/env.template` (no secrets)
- Code changes
- Documentation updates

⚠️ **If you accidentally commit secrets**:
1. **Immediately rotate** your APS credentials (create new app)
2. **Remove from git history**: `git filter-branch` or BFG Repo-Cleaner
3. **Notify your team**

---

## 🚀 You're All Set!

**Welcome to the team!** If you followed all steps, you should have:
- ✅ Working backend with Claude 4 Sonnet
- ✅ Working frontend
- ✅ Ability to generate TLA+ specifications
- ✅ Understanding of how to troubleshoot issues

**Questions?** Ask in your team chat or Slack support channel.

**Found a bug?** Open an issue in the repo.

**Happy validating!** 🎯

---

*Last updated: November 18, 2025*
*Version: 1.0*
*Status: Production-Ready*

