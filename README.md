# 🚀 HACKTIVATORS Recruitment Website

A complete, modern, technology-focused recruitment website built for school organization **HACKTIVATORS**.

Built with **Next.js (App Router)**, **React**, **TypeScript**, **Tailwind CSS**, and **Supabase**.

---

## 📁 Project Structure

```text
hacktivators-website/
├── .env.local.example       # Environment variable template
├── .env.local               # Local environment variables
├── package.json             # Project dependencies
├── tsconfig.json            # TypeScript configuration
├── tailwind.config.ts       # Custom tech theme styling
├── postcss.config.js        # PostCSS configuration
├── next.config.mjs          # Next.js configuration
├── supabase/
│   └── schema.sql           # Complete SQL script for table & RLS policies
└── src/
    ├── app/
    │   ├── page.tsx         # Home Page (Hero, slogan, sections showcase)
    │   ├── about/page.tsx   # About Page (Mission, vision, leadership notice)
    │   ├── sections/page.tsx# 4 Recruitment Sections breakdown
    │   ├── apply/page.tsx   # Multi-step application wizard (1-2 section picker)
    │   ├── success/page.tsx # Application confirmation & next steps
    │   ├── admin/page.tsx   # Protected Admin Dashboard (Auth, filter, status, notes)
    │   ├── layout.tsx       # Root layout with Header & Footer
    │   └── globals.css      # Custom tech grid & glassmorphism CSS
    ├── components/
    │   ├── Navbar.tsx       # Sticky responsive navbar with admin link
    │   ├── Footer.tsx       # Organization footer
    │   └── SectionCard.tsx  # Interactive section display card
    └── lib/
        ├── types.ts         # TypeScript interfaces & section constants
        └── supabase/
            └── client.ts    # Supabase browser client setup
```

---

## 🗄️ 1. Supabase Database SQL Setup

Run the following SQL snippet inside your **Supabase SQL Editor** ([https://app.supabase.com](https://app.supabase.com)):

```sql
-- 1. Create the applications table
CREATE TABLE IF NOT EXISTS public.applications (
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    full_name TEXT NOT NULL,
    age INTEGER NOT NULL,
    school_year TEXT NOT NULL,
    section_1 TEXT NOT NULL,
    section_2 TEXT,
    skill_level TEXT NOT NULL,
    experience TEXT,
    motivation TEXT NOT NULL,
    contribution TEXT NOT NULL,
    learning_goals TEXT NOT NULL,
    project_idea TEXT,
    availability TEXT NOT NULL,
    team_projects TEXT NOT NULL,
    status TEXT DEFAULT 'New' CHECK (status IN ('New', 'Under Review', 'Shortlisted', 'Interview / Trial', 'Accepted', 'Not Selected')),
    admin_notes TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT timezone('utc'::text, now()) NOT NULL
);

-- 2. Enable Row Level Security (RLS)
ALTER TABLE public.applications ENABLE ROW LEVEL SECURITY;

-- 3. Allow anyone (students) to submit applications (INSERT ONLY)
CREATE POLICY "Public insert access for student applications" 
ON public.applications 
FOR INSERT 
TO public 
WITH CHECK (true);

-- 4. Allow only authenticated admin users to view applications
CREATE POLICY "Admin read access for applications" 
ON public.applications 
FOR SELECT 
TO authenticated 
USING (true);

-- 5. Allow only authenticated admin users to update applications (status & notes)
CREATE POLICY "Admin update access for applications" 
ON public.applications 
FOR UPDATE 
TO authenticated 
USING (true)
WITH CHECK (true);

-- Indexes for search & filtering
CREATE INDEX IF NOT EXISTS idx_applications_status ON public.applications(status);
CREATE INDEX IF NOT EXISTS idx_applications_created_at ON public.applications(created_at DESC);
```

---

## ⚙️ 2. Required Supabase Settings & Admin Setup

1. **Create Supabase Account & Project**:
   - Go to [supabase.com](https://supabase.com) and create a project.
2. **Create Admin Auth User**:
   - In Supabase Dashboard, go to **Authentication** -> **Users**.
   - Click **Add User** -> **Create User**.
   - Enter your admin email (e.g. `admin@hacktivators.org`) and password.
   - Confirm the email address if required.
3. **Get API Credentials**:
   - Go to **Project Settings** -> **API**.
   - Copy **Project URL** (`https://xxx.supabase.co`).
   - Copy **anon public key** (`eyJhbGciOi...`).

---

## 🔐 3. Environment Variables (`.env.local`)

Create a `.env.local` file in the project root:

```env
NEXT_PUBLIC_SUPABASE_URL=https://your-project-ref.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-actual-anon-key-here
```

---

## 💻 4. Instructions for Running Locally

### Step 1: Install Dependencies
Open terminal in project root and run:
```bash
npm install
```

### Step 2: Start Development Server
```bash
npm run dev
```

### Step 3: Open in Browser
Navigate to `http://localhost:3000`.

- Public site: `http://localhost:3000`
- Application form: `http://localhost:3000/apply`
- Admin dashboard: `http://localhost:3000/admin` (Sign in with your admin credentials or password `admin` in dev mode).

---

## 🌐 5. Instructions for Deploying to Vercel

1. Push your repository to **GitHub** / **GitLab** / **Bitbucket**.
2. Go to [Vercel](https://vercel.com) and click **Add New Project**.
3. Import your repository.
4. Under **Environment Variables**, add:
   - Name: `NEXT_PUBLIC_SUPABASE_URL` | Value: `your-supabase-url`
   - Name: `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Value: `your-supabase-anon-key`
5. Click **Deploy**. Vercel will automatically build and deploy your live site!

---

## 🛡️ Security Features

- **No Service Key Exposure**: Uses public `anon` key only with Supabase RLS.
- **Form Validation**: Strict client-side step validation before submission.
- **Admin Privacy**: RLS policies enforce that public users cannot select or update applications or read admin notes.
