# Self-Hosted Supabase MCP Server - Cursor IDE Setup

This guide will help you integrate the Self-Hosted Supabase MCP server with Cursor IDE, giving you direct access to your Supabase database, authentication, and storage systems through AI assistance.

## What This Enables

With this MCP server, Cursor can help you:

- **Database Operations**: Query tables, execute SQL, analyze database stats
- **Schema Management**: List tables, apply migrations, manage extensions
- **Authentication**: Manage users, create/update/delete auth records
- **Storage**: Browse buckets and objects, manage file storage
- **TypeScript Generation**: Auto-generate types from your database schema
- **Real-time Features**: Inspect publications and realtime subscriptions

## Prerequisites

- Cursor IDE installed
- Self-hosted Supabase instance (via Coolify or Docker)
- Node.js 18+ installed
- Access to your Supabase credentials

## Step 1: Clone and Build the MCP Server

```bash
# Clone the repository
git clone https://github.com/HenkDz/selfhosted-supabase-mcp.git
cd selfhosted-supabase-mcp

# Install dependencies
npm install

# Build the server
npm run build
```

## Step 2: Configure Cursor IDE

### Option A: Global Configuration (Recommended)

1. Open Cursor IDE
2. Go to **Settings** (Cmd/Ctrl + ,)
3. Search for "MCP" or navigate to **Extensions > Model Context Protocol**
4. Click "Edit in settings.json" or open your Cursor settings file

Add this configuration to your `settings.json`:

```json
{
  "mcpServers": {
    "selfhosted-supabase": {
      "command": "node",
      "args": [
        "/absolute/path/to/selfhosted-supabase-mcp/dist/index.js",
        "--url", "https://your-supabase-url.com",
        "--anon-key", "your-anon-key-here",
        "--service-key", "your-service-key-here",
        "--db-url", "postgresql://user:password@host:port/database",
        "--jwt-secret", "your-jwt-secret-here"
      ]
    }
  }
}
```

### Option B: Project-Specific Configuration

Create a `.cursor/settings.json` file in your project root:

```json
{
  "mcpServers": {
    "selfhosted-supabase": {
      "command": "node",
      "args": [
        "./mcp-server/dist/index.js",
        "--url", "https://your-supabase-url.com",
        "--anon-key", "your-anon-key-here",
        "--service-key", "your-service-key-here",
        "--db-url", "postgresql://user:password@host:port/database",
        "--jwt-secret", "your-jwt-secret-here",
        "--tools-config", "./mcp-server/mcp-tools.json"
      ]
    }
  }
}
```

## Step 3: Get Your Supabase Credentials

### For Coolify-Managed Supabase:

Your credentials are typically located in:
- Docker Compose: `/data/coolify/services/[service-id]/docker-compose.yml`
- Environment file: `/data/coolify/services/[service-id]/.env`

Example values:
```bash
--url https://supabase.yourdomain.com
--anon-key eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...
--service-key eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...
--db-url postgres://supabase_admin:password@supabase.yourdomain.com:5432/postgres
--jwt-secret your-jwt-secret-here
```

### For Docker Compose Supabase:

Check your `docker-compose.yml` and `.env` files for:
- `SUPABASE_URL` or `API_EXTERNAL_URL`
- `SUPABASE_ANON_KEY`
- `SUPABASE_SERVICE_KEY` 
- `POSTGRES_PASSWORD` and connection details
- `JWT_SECRET`

## Step 4: Optional Tool Configuration

Create `mcp-tools.json` to limit which tools are available:

```json
{
  "allowedTools": [
    "list_tables",
    "execute_sql",
    "list_auth_users",
    "get_database_stats",
    "generate_typescript_types",
    "list_storage_buckets"
  ]
}
```

## Step 5: Test the Integration

1. Restart Cursor IDE
2. Open a project or create a new file
3. Start a chat with Cursor AI
4. Try these example prompts:

### Database Queries
```
Show me all tables in my database and their structure
```

```
Execute this SQL query: SELECT COUNT(*) FROM auth.users
```

```
What are the current database connection stats?
```

### Schema Management
```
Generate TypeScript types for my database schema and save them to ./types/database.ts
```

```
Show me all database extensions installed
```

### Authentication
```
List all users in my auth system
```

```
Create a new user with email test@example.com
```

### Storage
```
List all storage buckets and their contents
```

## Available MCP Tools

The server provides 18 tools:

### Database Tools
- `list_tables` - Show all database tables
- `list_extensions` - Show PostgreSQL extensions  
- `execute_sql` - Run custom SQL queries
- `get_database_stats` - Database performance metrics
- `get_database_connections` - Active connections

### Migration Tools
- `list_migrations` - Show applied migrations
- `apply_migration` - Apply new migrations safely

### Authentication Tools
- `list_auth_users` - List all users
- `get_auth_user` - Get specific user details
- `create_auth_user` - Create new users
- `update_auth_user` - Update user information
- `delete_auth_user` - Remove users

### Storage Tools
- `list_storage_buckets` - Show storage buckets
- `list_storage_objects` - Browse bucket contents

### Developer Tools
- `generate_typescript_types` - Auto-generate TS types
- `verify_jwt_secret` - Check JWT configuration
- `rebuild_hooks` - Restart database hooks
- `list_realtime_publications` - Show realtime config

### Configuration Tools
- `get_project_url` - Show project URL
- `get_anon_key` - Show anonymous key
- `get_service_key` - Show service key

## Example Prompts for Cursor

### Database Analysis
```
Analyze my database performance and show me any potential issues
```

### Schema Exploration
```
Show me the structure of my auth.users table and explain each column
```

### Type Generation
```
Generate TypeScript types for my entire database schema and explain how to use them in my Next.js app
```

### User Management
```
Show me all users created in the last 7 days and their registration method
```

### Storage Management
```
List all files in my 'avatars' storage bucket and check for any large files
```

### Migration Help
```
I need to add a new 'profiles' table. Show me the current schema and help me write a migration
```

## Troubleshooting

### Server Won't Start
- Check that Node.js 18+ is installed
- Verify all file paths are absolute, not relative
- Ensure the MCP server was built successfully (`npm run build`)

### Connection Issues
- Verify your Supabase URL is accessible
- Check that database port is correct (often 5432 or 5438)
- Ensure service key has proper permissions

### Permission Errors
- Use service role key for admin operations
- Check that database user has necessary permissions
- Verify JWT secret is correct

### Tool Restrictions
- Some tools require direct database connection (`--db-url`)
- Admin operations need service role key
- Check your `mcp-tools.json` if some tools are missing

## Security Notes

- Keep your service keys secure and never commit them to version control
- Use environment variables for sensitive credentials
- Consider using project-specific configuration for different environments
- Regularly rotate your keys and passwords

## Advanced Configuration

### Environment Variables
You can use environment variables instead of command line arguments:

```json
{
  "mcpServers": {
    "selfhosted-supabase": {
      "command": "node",
      "args": ["./mcp-server/dist/index.js"],
      "env": {
        "SUPABASE_URL": "https://your-supabase-url.com",
        "SUPABASE_ANON_KEY": "your-anon-key",
        "SUPABASE_SERVICE_KEY": "your-service-key",
        "DATABASE_URL": "postgresql://user:password@host:port/database",
        "JWT_SECRET": "your-jwt-secret"
      }
    }
  }
}
```

### Multiple Environments
Configure different environments:

```json
{
  "mcpServers": {
    "supabase-dev": {
      "command": "node",
      "args": ["./mcp-server/dist/index.js", "--url", "https://dev.supabase.com", "..."]
    },
    "supabase-prod": {
      "command": "node", 
      "args": ["./mcp-server/dist/index.js", "--url", "https://prod.supabase.com", "..."]
    }
  }
}
```

## Getting Help

If you encounter issues:

1. Check the [GitHub repository](https://github.com/HenkDz/selfhosted-supabase-mcp) for updates
2. Verify your Supabase instance is running and accessible
3. Test the MCP server independently before integrating with Cursor
4. Check Cursor's MCP documentation for IDE-specific issues

Happy coding with AI-powered Supabase integration! 🚀