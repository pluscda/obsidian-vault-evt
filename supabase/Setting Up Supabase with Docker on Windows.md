
## Prerequisites

### 1. Install WSL 2
```powershell
# Run as Administrator
wsl --update
wsl --install -d Debian
wsl --set-version Debian 2
```

### 2. Install Docker Desktop
- Download from https://docker.com
- Enable WSL 2 backend in settings
- Docker Desktop creates `docker-desktop` WSL distro automatically

---

## Install Supabase CLI (in WSL Debian)

```bash
# Enter Debian
wsl -d Debian

# Install as root
sudo su
apt-get update && apt-get install -y curl
curl -fsSL https://github.com/supabase/cli/releases/latest/download/supabase_linux_amd64.tar.gz -o /tmp/supabase.tar.gz
tar -xzf /tmp/supabase.tar.gz -C /usr/local/bin
rm /tmp/supabase.tar.gz
exit

# Verify
supabase --version
```

---

## Create & Start Supabase Project

```bash
# Create project folder
mkdir ~/my-supabase-project && cd ~/my-supabase-project

# Initialize Supabase
supabase init

# Start local Supabase (pulls ~2-3GB of Docker images first time)
supabase start
```

---

## Local Supabase URLs

| Service | URL | Port |
|---------|-----|------|
| API Gateway | http://localhost:54321 | 54321 |
| PostgreSQL | postgresql://postgres:postgres@localhost:54322/postgres | 54322 |
| Studio Dashboard | http://localhost:54323 | 54323 |

---

## Useful Commands

```bash
# Check status & get API keys
supabase status

# Push migrations to local DB
supabase db push --local

# Stop Supabase
supabase stop

# Reset database
supabase db reset
```

---

## Tips

- **SSL Error?** Use `--local` flag instead of `--db-url`
- **Password with special chars?** URL-encode them (`?` → `%3F`, `!` → `%21`, `/` → `%2F`)
- **Default local credentials:** `postgres:postgres`
- **API keys** are shown via `supabase status`

![[Pasted image 20260123105711.png]]![[Pasted image 20260123110151.png]]