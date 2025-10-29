# Calibre-Web + LazyLibrarian + qBittorrent Stack

Complete self-hosted ebook automation and management system for Kobo e-readers.

## Features

### Calibre-Web
- **Web-based ebook library management**
- **Kobo Sync**: Wirelessly sync books to your Kobo e-reader
- **OPDS catalog support**: Browse books on e-readers
- **Ebook conversion**: Convert between formats (with calibre mod)
- **User management**: Multiple users with custom reading lists

### LazyLibrarian
- **Automated book acquisition**: Monitor authors and auto-download new releases
- **Multiple search providers**: Supports GoodReads, GoogleBooks, and various torrent/NZB indexers
- **Quality preferences**: Choose preferred formats (EPUB, MOBI, PDF, etc.)
- **Author management**: Track authors and get notified of new books
- **Magazine support**: Download magazines automatically
- **Direct Calibre integration**: Automatically import to Calibre database

### qBittorrent
- **Torrent client**: Download books from legal sources
- **Web UI**: Manage torrents from browser
- **RSS support**: Automated torrent monitoring

## Setup

### Prerequisites

- Docker and Docker Compose installed
- Ports 8083, 8080, 5299, and 6881 available

### Quick Start

```bash
docker-compose up -d
```

Access the services:
- **Calibre-Web**: `http://localhost:8083`
- **qBittorrent**: `http://localhost:8080`
- **LazyLibrarian**: `http://localhost:5299`

**First Login:**
- Use the default credentials provided by the LinuxServer.io image
- See `.credentials` file (not in repo) for your configured username and password
- **Important:** Change the default credentials immediately after first login!

### Initial Configuration

1. **First Login**: Login with your credentials
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

### qBittorrent Setup

1. **First Login**: `http://<your-server-ip>:8080`
   - Default username: `admin`
   - Password: Check container logs: `docker logs qbittorrent`
2. **Change Password**: Tools → Options → Web UI → Authentication
3. **Configure Downloads**:
   - Tools → Options → Downloads
   - Default Save Path: `/downloads`
   - Keep incomplete in: `/downloads/incomplete`

### LazyLibrarian Setup

1. **First Login**: `http://<your-server-ip>:5299`
   - No default credentials required

2. **Initial Configuration**:
   - Go to **Config** (gear icon)
   - **Processing** tab:
     - eBook Folder: `/books`
     - Download Folder: `/downloads`
     - Check "Use eBook Folder for New Books"

3. **Add Download Client** (qBittorrent):
   - **Downloaders** tab:
     - Enable "qBittorrent"
     - Host: `qbittorrent` (container name)
     - Port: `8080`
     - Username/Password: From qBittorrent setup

4. **Configure Search Providers**:
   - **Providers** tab:
     - Enable search providers for legal/public domain books
     - Examples: Project Gutenberg, Standard Ebooks, Archive.org
     - Add torrent indexers that carry legal content

5. **Enable Calibre Integration** (Optional):
   - **Processing** tab:
     - Enable "Use Calibre"
     - Calibre Library Path: `/books`
     - This automatically imports books to Calibre database

6. **Configure Metadata Sources**:
   - **API Keys** tab:
     - Add GoodReads API key (optional, for better metadata)
     - Add GoogleBooks API key (optional)

### Automation Workflow

Once configured, the stack works automatically:

1. **Add Authors** in LazyLibrarian that you want to monitor
2. **LazyLibrarian searches** for books from those authors
3. **Click "Add to Wanted"** for books you want to download
4. **LazyLibrarian** automatically searches indexers and sends torrent to qBittorrent
5. **qBittorrent downloads** the book to `/downloads`
6. **LazyLibrarian moves** completed download to `/books`
7. **LazyLibrarian imports** to Calibre database (if enabled)
8. **Calibre-Web** automatically shows the new book
9. **Kobo sync** picks up the new book on next sync

## Directory Structure

```
calibreApp/
├── config/                     # Calibre-Web configuration
├── qbittorrent-config/         # qBittorrent configuration
├── lazylibrarian-config/       # LazyLibrarian configuration
├── books/                      # Your ebook library (shared across all services)
├── downloads/                  # Download location for qBittorrent
├── docker-compose.yml          # All three services
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
