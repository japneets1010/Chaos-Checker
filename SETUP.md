# Quick Setup Guide

## 🚀 Getting Started

### Frontend Setup (First Steps)

1. **Install dependencies:**
   ```bash
   cd frontend
   npm install
   ```

2. **Create environment file:**
   ```bash
   cp .env.local.example .env.local
   ```
   
   Edit `.env.local` and add:
   ```
   NEXT_PUBLIC_API_URL=http://localhost:8000
   ```

3. **Start the development server:**
   ```bash
   npm run dev
   ```

4. **Open your browser:**
   Navigate to http://localhost:3000

### Backend Setup (For Full Functionality)

1. **Create virtual environment:**
   ```bash
   cd backend
   python3 -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

2. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

3. **Create environment file:**
   ```bash
   cp .env.example .env
   ```
   
   Edit `.env` and add your API key:
   ```
   OPENAI_API_KEY=your_api_key_here
   LLM_PROVIDER=openai
   ```

4. **Start the backend server:**
   ```bash
   python app.py
   ```

## 🎨 Using the Application

1. **Draw your system:** Use the draw.io editor on the left to create your system diagram
2. **Select language:** Choose TLA+, PlusCal, or Fizzbee from the dropdown
3. **Validate:** Click "Validate Design" to check for issues
4. **Review results:** See any bugs, deadlocks, or inconsistencies in the right panel

## 📦 What's Included

- ✅ Draw.io editor integration
- ✅ Beautiful, modern UI with dark mode support
- ✅ Real-time validation
- ✅ Multiple specification languages (TLA+, PlusCal, Fizzbee)
- ✅ Detailed error explanations with traces

## 🔧 Troubleshooting

**Frontend won't start:**
- Make sure you're using Node.js 18+
- Delete `node_modules` and `.next`, then run `npm install` again

**Backend connection error:**
- Ensure the backend is running on port 8000
- Check that `.env.local` has the correct API URL

**Validation fails:**
- Make sure you have an API key configured in backend/.env
- Check backend logs for detailed error messages

