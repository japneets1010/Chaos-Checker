# 🚀 Chaos Checker - Quick Start Guide

Welcome to Chaos Checker! This guide will get you up and running in minutes.

## 📋 What You Need

- Node.js 18.17+ (for frontend)
- Python 3.9-3.13 (for backend)
- OpenAI API key OR Anthropic API key

## ⚡ Quick Setup (5 minutes)

### 1. Frontend Setup

```bash
cd frontend
npm install
npm run dev
```

Frontend will start on **http://localhost:3000**

### 2. Backend Setup

```bash
cd backend

# Create virtual environment
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Add your API key
# Edit backend/.env and replace 'your_openai_api_key_here' with your actual key
nano .env  # or use any text editor

# Start the backend
python app.py
```

Backend will start on **http://localhost:8000**

### 3. Add Your API Key

Edit `backend/.env`:

```bash
# For OpenAI (GPT-4)
OPENAI_API_KEY=sk-your-actual-key-here
LLM_PROVIDER=openai

# OR for Anthropic (Claude)
# ANTHROPIC_API_KEY=your-actual-key-here  
# LLM_PROVIDER=anthropic
```

## 🎯 How to Use

1. **Open** http://localhost:3000 in your browser
2. **Draw** your system architecture using draw.io:
   - States (circles)
   - Processes (rectangles)  
   - Transitions (arrows with labels)
3. **Select** your target language (TLA+, PlusCal, or Fizzbee)
4. **Click** "Validate Design"
5. **Review** the results:
   - Generated formal specification
   - Any bugs, deadlocks, or race conditions found
   - Detailed error traces

## 📖 Example Use Cases

### Example 1: Simple State Machine
1. Create 3 states: "idle", "processing", "done"
2. Connect them with arrows
3. Validate → Check for deadlocks

### Example 2: Distributed System  
1. Draw client and server processes
2. Add database component
3. Show message flows
4. Validate → Check for race conditions

### Example 3: Consensus Protocol
1. Draw multiple nodes
2. Show leader election flow
3. Validate → Check for split-brain scenarios

## 🔧 Troubleshooting

### Frontend won't start
```bash
# Check Node version (need 18.17+)
node --version

# If too old, upgrade Node.js
# Then try again
cd frontend
rm -rf node_modules .next
npm install
npm run dev
```

### Backend connection error
```bash
# Make sure backend is running on port 8000
lsof -ti:8000  

# If nothing, start it:
cd backend
source venv/bin/activate
python app.py
```

### "API key not found" error
- Check that you edited `backend/.env`
- Restart the backend after adding the key
- Make sure you uncommented the OPENAI_API_KEY line

### Validation takes forever
- LLM is generating the specification (can take 10-30 seconds)
- Check backend logs for progress
- Try a simpler diagram first

## 🎨 Tips for Better Results

1. **Label Everything** - Clear labels help the LLM understand your design
2. **Use Standard Shapes**:
   - Circles = States
   - Rectangles = Processes  
   - Diamonds = Decisions
3. **Show Transitions** - Label arrows with action names
4. **Keep It Simple** - Start with 3-5 components
5. **Be Specific** - "acquire_lock" is better than "do something"

## 🔥 Common Errors Detected

- ✅ Deadlocks - System gets stuck
- ✅ Race Conditions - Concurrent access issues
- ✅ Invariant Violations - Invalid states reached
- ✅ Liveness Problems - Progress guarantees broken
- ✅ Lost Updates - Concurrent write conflicts

## 📚 Next Steps

- Try different specification languages (TLA+ vs PlusCal vs Fizzbee)
- Experiment with complex distributed systems
- Check the generated specs to learn formal verification
- Modify diagrams based on error feedback

## 🆘 Need Help?

- Check backend logs: `tail -f backend/logs/*.log` (if logging is enabled)
- Verify API key is valid: `echo $OPENAI_API_KEY`
- Test backend directly: `curl http://localhost:8000`
- Check frontend console for errors (F12 in browser)

## 🚀 You're Ready!

Visit http://localhost:3000 and start validating your designs!

Happy hacking! 🎉

