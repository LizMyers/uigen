# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

UIGen is an AI-powered React component generator with live preview. It uses Next.js 15 with App Router, React 19, TypeScript, and Tailwind CSS v4. The application features a virtual file system for component generation and editing without writing files to disk.

## Common Commands

### Development
- `npm run dev` - Start development server with Turbopack
- `npm run dev:daemon` - Start server as daemon with logs to logs.txt
- `npm run build` - Build for production
- `npm run start` - Start production server

### Testing and Code Quality
- `npm run test` - Run Vitest tests
- `npm run lint` - Run ESLint

### Database and Setup
- `npm run setup` - Install dependencies, generate Prisma client, and run migrations
- `npm run db:reset` - Reset database with force flag

## Architecture

### Core Components

**Virtual File System (`src/lib/file-system.ts`)**
- `VirtualFileSystem` class manages in-memory file operations
- Supports create, read, update, delete, rename operations
- Serialization/deserialization for persistence
- No actual files written to disk during development

**AI Chat Integration (`src/app/api/chat/route.ts`)**
- Uses Anthropic Claude via AI SDK
- Implements `str_replace_editor` and `file_manager` tools
- Streams responses with file system updates
- Supports both authenticated and anonymous usage

**Authentication System**
- Basic email/password auth with bcrypt
- Session management with JWT (jose library)
- Anonymous user support for trying the app
- User projects stored in SQLite via Prisma

**Project Structure**
- `src/actions/` - Server actions for project CRUD
- `src/components/` - UI components organized by feature
- `src/lib/contexts/` - React contexts for file system and chat
- `src/lib/tools/` - AI tool implementations
- `prisma/` - Database schema and migrations

### Key Features

1. **Live Preview**: Components render in real-time as they're generated
2. **Code Editor**: Monaco editor with syntax highlighting
3. **File Tree**: Navigate virtual file system
4. **Project Persistence**: Authenticated users can save projects
5. **Export**: Generated code can be exported

### Database Schema

- `User` - Basic user accounts
- `Project` - Contains serialized messages and file system data
- Uses SQLite with Prisma ORM
- Generated client in `src/generated/prisma/`

### Testing

- Vitest for unit tests
- React Testing Library for component tests
- Test files in `__tests__/` directories
- Run single test: `npm run test -- path/to/test.test.ts`

## Development Notes

### Environment Variables
- `ANTHROPIC_API_KEY` - Optional, app works without it (returns static code)
- No API key = mock responses instead of actual AI generation

### File System Context
- All file operations go through `FileSystemProvider`
- Context manages selected file state and triggers re-renders
- Virtual file system persists in database for authenticated users

### AI Tool Integration
- Tools are defined in `src/lib/tools/`
- `str_replace_editor` for file content operations
- `file_manager` for file system operations
- Tools integrate with virtual file system