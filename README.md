# ISET AI Assistant

A student-focused AI chatbot platform for students at the International School of Economics at Tbilisi State University (ISET).

## Features

- **AI Chatbot**: Powered by Google Gemini, optimized for economics and statistics students.
- **Academic Support**: Step-by-step explanations for 1st-year university concepts.
- **Clean Interface**: Minimal, academic design inspired by university standards.
- **Full-Stack Architecture**: React frontend with an Express backend for production serving.

## Tech Stack

- **Frontend**: React, TailwindCSS, Motion/React, Lucide Icons, React Router.
- **Backend**: Node.js, Express.
- **AI**: @google/genai (Gemini 3 Flash).

## Prerequisites

- Node.js (v18 or higher)
- npm or yarn
- Gemini API Key

## Setup & Local Development

1. **Clone the repository** (if applicable).
2. **Install dependencies**:
   ```bash
   npm install
   ```
3. **Configure Environment Variables**:
   Create a `.env` file in the root directory and add your Gemini API key:
   ```env
   GEMINI_API_KEY="your_api_key_here"
   ```
4. **Run the development server**:
   ```bash
   npm run dev
   ```
   The app will be available at `http://localhost:3000`.

## Building for Production

1. **Build the project**:
   ```bash
   npm run build
   ```
   This command builds the React frontend with Vite and bundles the Express server with esbuild.

2. **Start the production server**:
   ```bash
   npm start
   ```

## Project Structure

- `src/`: React source code.
  - `components/`: UI components (Home, Chat, About, Navbar).
  - `App.tsx`: Main router and layout.
- `server.ts`: Express backend entry point.
- `vite.config.ts`: Vite configuration with environment variable injection.
- `package.json`: Dependency and script management.

---

Developed for ISET Students.
