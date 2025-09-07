# 🎓 AI Digital Tutor - Complete Setup Guide

## Overview
This guide will help you set up the AI Digital Tutor project on a new laptop. The project consists of a React frontend with 3D avatar and a Node.js backend for AI chat functionality.

## 📋 Prerequisites

### Required Software
1. **Node.js** (v18 or higher)
   - Download from: https://nodejs.org/
   - Choose LTS version
   - Verify installation: `node --version` and `npm --version`

2. **Git** 
   - Download from: https://git-scm.com/
   - Verify installation: `git --version`

3. **Code Editor** (Recommended: VS Code)
   - Download from: https://code.visualstudio.com/

### Required API Keys
You'll need to obtain these API keys:

1. **OpenAI API Key**
   - Go to: https://platform.openai.com/api-keys
   - Create account and generate API key
   - Note: This requires a paid OpenAI account

2. **ElevenLabs API Key**
   - Go to: https://elevenlabs.io/
   - Create account and get API key from profile
   - Note: Free tier available with limited characters

3. **Supabase Project** (for chat persistence)
   - Go to: https://supabase.com/
   - Create new project
   - Get project URL and anon key from settings

## 🚀 Step-by-Step Installation

### Step 1: Clone the Repository
```bash
git clone https://github.com/maazajaz/ai-tutor-full-stack.git
cd ai-tutor-full-stack
```

### Step 2: Install Dependencies

#### Frontend Dependencies
```bash
npm install
```

#### Backend Dependencies
```bash
cd server
npm install
cd ..
```

### Step 3: Environment Configuration

#### Backend Environment (.env in server folder)
Create `server/.env` file with the following content:
```env
# OpenAI Configuration
OPENAI_API_KEY=your_openai_api_key_here

# ElevenLabs Configuration
ELEVENLABS_API_KEY=your_elevenlabs_api_key_here

# Server Configuration
PORT=3000
NODE_ENV=development

# CORS Configuration (for frontend)
CORS_ORIGIN=http://localhost:5173
```

#### Frontend Environment (.env in root folder)
Create `.env` file in the root directory:
```env
# Backend API URL
VITE_API_URL=http://localhost:3000

# Supabase Configuration
VITE_SUPABASE_URL=your_supabase_project_url
VITE_SUPABASE_ANON_KEY=your_supabase_anon_key

# Environment
VITE_NODE_ENV=development
```

### Step 4: Supabase Database Setup

#### 4.1 Create Supabase Tables
Execute these SQL commands in Supabase SQL Editor:

```sql
-- Enable RLS (Row Level Security)
create table public.chat_sessions (
  id text primary key,
  user_id uuid references auth.users(id) on delete cascade,
  title text not null default 'New Chat',
  messages jsonb default '[]'::jsonb,
  notes text default '',
  created_at timestamp with time zone default timezone('utc'::text, now()) not null,
  updated_at timestamp with time zone default timezone('utc'::text, now()) not null
);

-- Enable RLS
alter table public.chat_sessions enable row level security;

-- Create policies
create policy "Users can view their own chat sessions"
  on public.chat_sessions for select
  using (auth.uid() = user_id);

create policy "Users can create their own chat sessions"
  on public.chat_sessions for insert
  with check (auth.uid() = user_id);

create policy "Users can update their own chat sessions"
  on public.chat_sessions for update
  using (auth.uid() = user_id);

create policy "Users can delete their own chat sessions"
  on public.chat_sessions for delete
  using (auth.uid() = user_id);
```

#### 4.2 Configure Supabase Authentication
1. Go to Authentication > Settings in Supabase dashboard
2. Enable Email authentication
3. Configure site URL: `http://localhost:5173`
4. Add redirect URLs: `http://localhost:5173/auth/callback`

### Step 5: Project Structure Verification
Ensure your project has this structure:
```
ai-tutor-full-stack/
├── frontend/
│   ├── public/
│   │   ├── models/
│   │   │   ├── 64f1a714fe61576b46f27ca2.glb  # Main avatar
│   │   │   └── animations.glb                # Avatar animations
│   │   └── animations/                       # Additional animations
│   └── src/
│       ├── components/
│       ├── hooks/
│       └── lib/
├── server/
│   ├── server.js
│   ├── package.json
│   └── .env
├── bin/
│   └── rhubarb.exe                          # Lip-sync tool
├── audios/                                  # Generated audio files
├── package.json
├── .env
└── COMPLETE_SETUP_GUIDE.md
```

## 🏃‍♂️ Running the Application

### Terminal 1: Start Backend Server
```bash
cd server
npm start
```
Expected output:
```
🔧 Environment Check:
OpenAI API Key present: true
ElevenLabs API Key present: true
Environment: development
🎓 AI Digital Tutor listening on port 3000
🌐 Frontend: http://localhost:5173
🤖 Backend: http://localhost:3000
```

### Terminal 2: Start Frontend Development Server
```bash
npm run dev
```
Expected output:
```
  VITE v4.x.x  ready in xxx ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
```

### Step 6: Verify Installation

#### 6.1 Check Backend Health
Visit: http://localhost:3000
Should show: "🎓 AI Digital Tutor API - Ready to help students learn!"

#### 6.2 Check Frontend
Visit: http://localhost:5173
Should show the 3D avatar and chat interface

#### 6.3 Test API Keys
1. Check ElevenLabs: http://localhost:3000/elevenlabs-status
2. Try sending a chat message to verify OpenAI integration

## 🔧 Troubleshooting

### Common Issues

#### Issue 1: "Module not found" errors
**Solution:**
```bash
# Clear node_modules and reinstall
rm -rf node_modules package-lock.json
npm install

# For server
cd server
rm -rf node_modules package-lock.json
npm install
```

#### Issue 2: Port already in use
**Solution:**
```bash
# Kill processes on ports 3000 and 5173
npx kill-port 3000
npx kill-port 5173
```

#### Issue 3: CORS errors
**Solution:**
- Ensure backend .env has `CORS_ORIGIN=http://localhost:5173`
- Restart backend server after changing .env

#### Issue 4: Avatar not loading
**Solution:**
- Verify files exist in `public/models/`
- Check browser console for 404 errors
- Ensure GLTF model files are not corrupted

#### Issue 5: API key errors
**Solution:**
- Verify all API keys are correctly set in .env files
- Check for extra spaces or quotes in API keys
- Restart servers after changing .env files

#### Issue 6: Supabase connection issues
**Solution:**
- Verify Supabase URL and keys in frontend .env
- Check if tables are created correctly
- Verify RLS policies are set up

### Debug Commands

#### Check Environment Variables
```bash
# Backend
cd server
node -e "console.log(require('dotenv').config())"

# Frontend (check .env is loaded)
cat .env
```

#### Test API Endpoints
```bash
# Test backend health
curl http://localhost:3000

# Test ElevenLabs integration
curl http://localhost:3000/elevenlabs-status

# Test chat endpoint
curl -X POST http://localhost:3000/api/chat \
  -H "Content-Type: application/json" \
  -d '{"message":"hello"}'
```

## 📱 Features Overview

### Core Features
- ✅ 3D Avatar with lip-sync animation
- ✅ AI-powered tutoring (OpenAI GPT)
- ✅ Voice synthesis (ElevenLabs)
- ✅ Multi-language support (English/Hinglish)
- ✅ Chat history persistence (Supabase)
- ✅ User authentication
- ✅ Programming/CS focused questions
- ✅ Notes generation

### Tech Stack
- **Frontend**: React, Vite, Three.js, Tailwind CSS
- **Backend**: Node.js, Express.js
- **AI**: OpenAI GPT-3.5/4, ElevenLabs TTS
- **Database**: Supabase (PostgreSQL)
- **3D**: GLTF models, animations, lip-sync

## 📝 Development Notes

### Important Files
- `src/components/Avatar.jsx` - 3D avatar component with lip-sync
- `src/hooks/useChat.jsx` - Chat logic and API integration
- `src/components/UI.jsx` - User interface components
- `server/server.js` - Backend API server
- `bin/rhubarb.exe` - Lip-sync generation tool

### API Endpoints
- `POST /api/chat` - Main chat endpoint
- `GET /elevenlabs-status` - Check ElevenLabs API status
- `POST /api/generate-notes` - Generate AI notes from chat

### Environment Variables Reference
| Variable | Description | Required |
|----------|-------------|----------|
| `OPENAI_API_KEY` | OpenAI API key for chat | Yes |
| `ELEVENLABS_API_KEY` | ElevenLabs API key for voice | Yes |
| `VITE_SUPABASE_URL` | Supabase project URL | Yes |
| `VITE_SUPABASE_ANON_KEY` | Supabase anonymous key | Yes |
| `VITE_API_URL` | Backend API URL | Yes |

## 🚀 Deployment Notes

### For Production Deployment
1. Update CORS_ORIGIN in backend .env
2. Update VITE_API_URL in frontend .env
3. Configure Supabase site URL for production domain
4. Ensure all environment variables are set in hosting platform

### Performance Optimization
- Avatar models are optimized for web
- Chat history limited to last 10 messages for API calls
- Backend payload limit increased to 50MB for large histories

## 🆘 Support

If you encounter issues:
1. Check this troubleshooting guide first
2. Verify all prerequisites are installed
3. Ensure all environment variables are correctly set
4. Check browser console and terminal logs for errors
5. Restart both frontend and backend servers

## 🎯 Quick Start Checklist

- [ ] Install Node.js, Git, VS Code
- [ ] Clone repository
- [ ] Install dependencies (root + server)
- [ ] Get API keys (OpenAI, ElevenLabs, Supabase)
- [ ] Create .env files (root + server)
- [ ] Set up Supabase database tables
- [ ] Start backend server (Terminal 1)
- [ ] Start frontend server (Terminal 2)
- [ ] Test application at http://localhost:5173
- [ ] Verify avatar loads and chat works

---

**🎓 Happy Learning with AI Digital Tutor!**
