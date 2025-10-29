# Calibre-Web for Kobo E-Reader

Self-hosted Calibre-Web instance for managing and syncing ebooks with Kobo e-readers.

## Features

- **Web-based ebook library management**
- **Kobo Sync**: Wirelessly sync books to your Kobo e-reader
- **OPDS catalog support**: Browse books on e-readers
- **Ebook conversion**: Convert between formats (with calibre mod)
- **User management**: Multiple users with custom reading lists

## Setup

### Prerequisites

- Docker and Docker Compose installed
- Port 8083 available

### Quick Start

```bash
docker-compose up -d
```

Access Calibre-Web at: `http://localhost:8083`

**Default credentials:**
- Username: `admin`
- Password: `admin123`

**Important:** Change the default password immediately after first login!

### Initial Configuration

1. **First Login**: Login with default credentials
2. **Database Location**: Point to `/books` directory
3. **Enable Kobo Sync**:
   - Go to Admin → Edit Basic Configuration
   - Expand "Feature Configuration"
   - Check "Enable Kobo sync"
   - Check "Proxy unknown requests to Kobo Store"
   - Set "Server External Port" to `8083`
   - Save settings

### Kobo E-Reader Setup

1. In Calibre-Web, go to your user profile
2. Find "Kobo Sync Token" section
3. Click "Create/View" to get your sync URL
4. On your Kobo:
   - Go to Settings → Advanced Settings
   - Find the Kobo Store sync URL
   - Replace it with your Calibre-Web sync URL

**Sync URL format:**
```
http://<your-server-ip>:8083/kobo/<your-token>
```

## Directory Structure

```
calibreApp/
├── config/          # Calibre-Web configuration
├── books/           # Your ebook library
├── docker-compose.yml
└── .github/workflows/deploy.yml
```

## Adding Books

1. Place ebook files in the `books/` directory
2. In Calibre-Web, go to Admin → Reconnect Database
3. Books will appear in your library

## Backup

Important directories to backup:
- `./config` - Calibre-Web settings and database
- `./books` - Your ebook library

## Troubleshooting

### Container won't start
```bash
docker-compose logs
```

### Can't connect to Kobo
- Ensure port 8083 is accessible from your network
- Check firewall rules allow port 8083
- Verify the sync URL is correct on your Kobo

### Database errors
- Ensure `books/` directory has proper permissions
- Check `config/` directory is writable

## Documentation

- [Calibre-Web Documentation](https://github.com/janeczku/calibre-web/wiki)
- [Kobo Sync Setup Guide](https://github.com/janeczku/calibre-web/wiki/Kobo-Integration)
- [LinuxServer.io Calibre-Web](https://docs.linuxserver.io/images/docker-calibre-web/)

## Tech Stack

- **Calibre-Web**: Web app for browsing, reading and downloading ebooks
- **LinuxServer.io**: Docker image maintainer
- **Docker**: Containerization
- **GitHub Actions**: Automated deployment
