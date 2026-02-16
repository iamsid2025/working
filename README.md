# Next.js Project Setup Guide

This guide will help you set up and run a Next.js project from scratch.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Creating a New Next.js Project](#creating-a-new-nextjs-project)
- [Project Structure](#project-structure)
- [Running the Development Server](#running-the-development-server)
- [Building for Production](#building-for-production)
- [Deployment](#deployment)
- [Environment Variables](#environment-variables)
- [Additional Resources](#additional-resources)

## Prerequisites

Before you begin, ensure you have the following installed on your system:

- **Node.js** (version 18.17 or later)
  - Download from [nodejs.org](https://nodejs.org/)
  - Verify installation: `node --version`
- **npm** (comes with Node.js) or **yarn** or **pnpm**
  - Verify npm: `npm --version`
  - Or install yarn: `npm install -g yarn`
  - Or install pnpm: `npm install -g pnpm`

## Creating a New Next.js Project

### Using npx (Recommended)

```bash
npx create-next-app@latest my-app
```

### Using yarn

```bash
yarn create next-app my-app
```

### Using pnpm

```bash
pnpm create next-app my-app
```

### Interactive Setup

During the setup, you'll be prompted to configure:

- TypeScript: Would you like to use TypeScript? (Yes/No)
- ESLint: Would you like to use ESLint? (Yes/No)
- Tailwind CSS: Would you like to use Tailwind CSS? (Yes/No)
- `src/` directory: Would you like to use `src/` directory? (Yes/No)
- App Router: Would you like to use App Router? (recommended) (Yes/No)
- Import alias: Would you like to customize the default import alias? (Yes/No)

### Manual Installation

If you prefer to set up manually:

```bash
mkdir my-app
cd my-app
npm init -y
npm install next@latest react@latest react-dom@latest
```

Then add scripts to your `package.json`:

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  }
}
```

## Project Structure

A typical Next.js project structure:

```
my-app/
├── app/                  # App Router (Next.js 13+)
│   ├── layout.tsx       # Root layout
│   ├── page.tsx         # Home page
│   └── globals.css      # Global styles
├── public/              # Static files (images, fonts, etc.)
├── node_modules/        # Dependencies
├── package.json         # Project dependencies and scripts
├── next.config.js       # Next.js configuration
├── tsconfig.json        # TypeScript configuration
└── .gitignore          # Git ignore rules
```

Or with Pages Router:

```
my-app/
├── pages/               # Pages Router
│   ├── _app.tsx        # Custom App component
│   ├── _document.tsx   # Custom Document
│   ├── index.tsx       # Home page
│   └── api/            # API routes
├── public/             # Static files
├── styles/             # CSS files
├── components/         # React components
└── ...
```

## Running the Development Server

Navigate to your project directory and start the development server:

```bash
cd my-app
npm run dev
# or
yarn dev
# or
pnpm dev
```

The application will be available at [http://localhost:3000](http://localhost:3000)

### Custom Port

To run on a different port:

```bash
npm run dev -- -p 3001
# or
next dev -p 3001
```

## Building for Production

### Create an optimized production build:

```bash
npm run build
```

This command:
- Generates an optimized production build
- Outputs static HTML files and JavaScript bundles
- Creates a `.next` folder with the build output

### Start the production server:

```bash
npm run start
```

The production server will run at [http://localhost:3000](http://localhost:3000)

## Deployment

### Vercel (Recommended)

Next.js is developed by Vercel, making it the easiest deployment option:

1. Push your code to a Git repository (GitHub, GitLab, or Bitbucket)
2. Import your project on [Vercel](https://vercel.com)
3. Vercel will automatically detect Next.js and configure the build settings
4. Click "Deploy"

### Other Platforms

Next.js can be deployed to:

- **Netlify**: Connect your Git repository and deploy
- **AWS Amplify**: Use the Amplify Console
- **Docker**: Create a Dockerfile and deploy to any container platform
- **Static Export**: For static sites, add `output: 'export'` to `next.config.js`
  - Note: Static export doesn't support SSR, ISR, API routes, or dynamic image optimization

### Docker Deployment

Example Dockerfile:

```dockerfile
FROM node:18-alpine AS base

# Install dependencies only when needed
FROM base AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci

# Rebuild the source code only when needed
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

# Production image
FROM base AS runner
WORKDIR /app
ENV NODE_ENV production
COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static

EXPOSE 3000
CMD ["node", "server.js"]
```

## Environment Variables

### Local Development

Create a `.env.local` file in the root directory:

```bash
# .env.local
DATABASE_URL=your_database_url
API_KEY=your_api_key
NEXT_PUBLIC_API_URL=https://api.example.com
```

**Note**: Variables prefixed with `NEXT_PUBLIC_` are exposed to the browser.

### Environment Files

- `.env` - Default environment variables
- `.env.local` - Local overrides (gitignored)
- `.env.development` - Development environment
- `.env.production` - Production environment

### Usage in Code

```typescript
// Server-side only
const dbUrl = process.env.DATABASE_URL;

// Available in browser
const apiUrl = process.env.NEXT_PUBLIC_API_URL;
```

## Key Features

### App Router (Next.js 13+)

- **Server Components**: Components render on the server by default
- **Streaming**: Progressive rendering and faster page loads
- **Data Fetching**: Simplified data fetching with async/await
- **Layouts**: Shared UI across routes
- **Loading States**: Built-in loading UI
- **Error Handling**: Error boundaries for better UX

### Pages Router (Traditional)

- **File-based Routing**: Create routes by adding files to `pages/`
- **API Routes**: Build API endpoints in the `pages/api/` directory
- **Dynamic Routes**: Use `[param].tsx` for dynamic segments
- **Static Generation**: Pre-render pages at build time
- **Server-Side Rendering**: Render pages on each request

### Image Optimization

```tsx
import Image from 'next/image';

<Image
  src="/profile.jpg"
  alt="Profile"
  width={500}
  height={500}
/>
```

### Font Optimization

```tsx
import { Inter } from 'next/font/google';

const inter = Inter({ subsets: ['latin'] });

export default function RootLayout({ children }) {
  return (
    <html lang="en" className={inter.className}>
      <body>{children}</body>
    </html>
  );
}
```

## Additional Resources

- **Official Documentation**: [nextjs.org/docs](https://nextjs.org/docs)
- **Learn Next.js**: [nextjs.org/learn](https://nextjs.org/learn)
- **Next.js Examples**: [github.com/vercel/next.js/tree/canary/examples](https://github.com/vercel/next.js/tree/canary/examples)
- **Next.js Discord**: [nextjs.org/discord](https://nextjs.org/discord)
- **GitHub Repository**: [github.com/vercel/next.js](https://github.com/vercel/next.js)

## Troubleshooting

### Common Issues

**Port already in use:**
```bash
# Use a different port (easiest solution)
npm run dev -- -p 3001

# Or kill the process gracefully (Unix/Linux/Mac)
# First, try graceful termination
lsof -ti:3000 | xargs kill -TERM 2>/dev/null

# Wait 5-10 seconds, then check if process is still running
lsof -ti:3000

# If process is still running, force kill
lsof -ti:3000 | xargs kill -9 2>/dev/null

# Or kill the process (Windows)
# Find the process: netstat -ano | findstr :3000
# Kill it: taskkill /PID <PID> /F

# Or use cross-platform solution
npx kill-port 3000
```

**Module not found:**
```bash
# Recommended: Reinstall with existing lock file for consistency
rm -rf node_modules
npm ci

# ⚠️ WARNING: Only as absolute last resort - deleting package-lock.json 
# can cause version inconsistencies across environments
# rm -rf node_modules package-lock.json
# npm install
```

**Build errors:**
```bash
# Clear Next.js cache
rm -rf .next
npm run build
```

## Contributing

Feel free to contribute to this project by submitting issues or pull requests.

## License

This project is open source and available under the [MIT License](LICENSE).