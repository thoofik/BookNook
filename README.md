# BookNook

BookNook is a modern book tracking application built with Next.js, Tailwind CSS, and Supabase. It allows users to manage their book collections, reading lists, challenges, and more.

## Features

- Book management (add, edit, delete)
- Reading lists
- Reading challenges
- Genre classification
- Literary trope tracking
- User profiles
- Authentication via Supabase Auth

## Tech Stack

- **Frontend**: Next.js, TypeScript, Tailwind CSS
- **Backend**: Supabase (PostgreSQL)
- **Authentication**: Supabase Auth
- **Hosting**: Vercel

## Getting Started

### Prerequisites

- Node.js (v16 or later)
- npm or yarn
- A Supabase account

### Setup

1. Clone the repository
```bash
git clone <repository-url>
cd booknook
```

2. Install dependencies
```bash
npm install
# or
yarn install
```

3. Create a Supabase project
   - Go to [supabase.com](https://supabase.com/) and create a new project
   - Create the following tables in Supabase SQL editor:

```sql
-- Books table
CREATE TABLE books (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  title TEXT NOT NULL,
  author TEXT NOT NULL,
  isbn TEXT,
  cover_image TEXT,
  format TEXT CHECK (format IN ('Physical', 'Digital', 'Audio')),
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  user_id UUID REFERENCES auth.users(id)
);

-- Genres table
CREATE TABLE genres (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  name TEXT NOT NULL UNIQUE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Tropes table
CREATE TABLE tropes (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  name TEXT NOT NULL UNIQUE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Reading lists table
CREATE TABLE reading_lists (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  name TEXT NOT NULL,
  description TEXT,
  user_id UUID REFERENCES auth.users(id),
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Reading list items table
CREATE TABLE reading_list_items (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  reading_list_id UUID REFERENCES reading_lists(id) ON DELETE CASCADE,
  book_id UUID REFERENCES books(id) ON DELETE CASCADE,
  current_page INTEGER DEFAULT 0,
  status TEXT CHECK (status IN ('To Read', 'Reading', 'Completed', 'DNF')),
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Challenges table
CREATE TABLE challenges (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  name TEXT NOT NULL,
  description TEXT,
  start_date DATE,
  end_date DATE,
  goal_count INTEGER,
  user_id UUID REFERENCES auth.users(id),
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Challenge books table
CREATE TABLE challenge_books (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  challenge_id UUID REFERENCES challenges(id) ON DELETE CASCADE,
  book_id UUID REFERENCES books(id) ON DELETE CASCADE,
  completed_at TIMESTAMP WITH TIME ZONE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Book genres junction table
CREATE TABLE book_genres (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  book_id UUID REFERENCES books(id) ON DELETE CASCADE,
  genre_id UUID REFERENCES genres(id) ON DELETE CASCADE,
  UNIQUE(book_id, genre_id)
);

-- Book tropes junction table
CREATE TABLE book_tropes (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  book_id UUID REFERENCES books(id) ON DELETE CASCADE,
  trope_id UUID REFERENCES tropes(id) ON DELETE CASCADE,
  UNIQUE(book_id, trope_id)
);
```

4. Set up environment variables
   - Create a `.env.local` file in the root directory
   - Add the following variables:
```
NEXT_PUBLIC_SUPABASE_URL=your-supabase-url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-supabase-anon-key
```

5. Run the development server
```bash
npm run dev
# or
yarn dev
```

6. Open [http://localhost:3000](http://localhost:3000) in your browser to see the application.

## Deployment

The easiest way to deploy the application is using [Vercel](https://vercel.com).

1. Push your code to a GitHub repository
2. Import the project into Vercel
3. Set the environment variables
4. Deploy!

## License

This project is licensed under the MIT License. 