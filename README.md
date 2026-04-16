### Setup Photo Gallery Arranger with docker-compose.yml
docker run -p 5000:5000 -v C:\Path\To\Photos:/input -v C:\Path\To\Output:/output tomasburkon/sort-photos-portable:latest
-----------------------------------------

### Easy Setup with Docker Compose
1. Create a `docker-compose.yml` file anywhere on your system.
2. Paste the following configuration into the file:
```yaml
services:
  photo-gallery:
    image: tomasburkon/sort-photos-portable:latest
    container_name: photo-gallery
    ports:
      - "5000:5000"
    volumes:
      - ./input:/input        # Drop your photos here
      - ./output:/output      # Organized albums appear here
      - ./data:/data          # Persistent metadata state
    restart: unless-stopped
```
3. Open your terminal in the same folder and run:
`docker compose up -d`
4. The dashboard will be available at `http://localhost:5000`

-----------------------------------------

# 📸 Photo Gallery — Portable Edition

A self-contained, Dockerized photo organizer. Drop your phone photos in, and the app sorts them into location/date-based albums. Browse, merge, rename, and manage your albums through a beautiful web UI — then upload the organized folders to your NAS.

**No AI, no API keys, no external services required.** Just Docker.

---

## Quick Start

### 1. Prerequisites

- [Docker](https://docs.docker.com/get-docker/) installed on your machine

### 2. Run

```bash
# Clone or copy this project
cd sort-photos-portable

# Start the application
docker compose up -d
```

### 3. Use

1. **Drop photos** into the `./input/` folder (JPEG files)
2. **Open** [http://localhost:5000](http://localhost:5000) in your browser
3. **Click** "Start Processing" — the app extracts EXIF metadata and sorts photos into albums by location and date
4. **Browse** your organized albums in the Gallery
5. **Merge** folders, **rename** albums, or **delete** photos as needed
6. **Upload** the `./output/` folder contents to your NAS (Synology, etc.)

### 4. Stop

```bash
docker compose down
```

---

## How It Works

```
📂 ./input/              📂 ./output/
├── IMG_001.jpg          ├── Prague_20240723/
├── IMG_002.jpg    →     │   ├── IMG_001.jpg
├── IMG_003.jpg          │   └── IMG_002.jpg
└── IMG_004.jpg          └── Vienna_20240725/
                              ├── IMG_003.jpg
                              └── IMG_004.jpg
```

The app reads EXIF metadata (GPS coordinates, date/time, camera info) from your photos and:
1. **Reverse-geocodes** GPS coordinates to get city/country names
2. **Groups** photos by location and date
3. **Creates** organized folders with the format `{City}_{YYYYMMDD}`

---

## Directory Structure

| Directory | Purpose |
|-----------|---------|
| `./input/` | Drop your source photos here |
| `./output/` | Organized albums appear here |
| `./data/` | Internal metadata state (auto-managed) |

All three directories are Docker volume mounts — your data stays on your host machine.

---

## Features

- 📸 **EXIF Metadata Extraction** — date, GPS, camera info
- 🗺️ **Reverse Geocoding** — converts GPS to city/country names
- 📁 **Auto-sorting** — organizes by `City_YYYYMMDD` folders
- 🔀 **Merge Folders** — combine albums into date ranges
- ✏️ **Rename Albums** — change location names
- 🗑️ **Delete** — remove individual photos or entire albums
- 🖼️ **Image Viewer** — lightbox, keyboard navigation, thumbnail strip
- 📱 **Responsive** — works on desktop and mobile browsers

---

## Configuration

The app works with zero configuration. For advanced use:

| Environment Variable | Default | Description |
|---------------------|---------|-------------|
| `INPUT_DIR` | `/input` | Source photo directory |
| `OUTPUT_DIR` | `/output` | Organized output directory |
| `DATA_DIR` | `/data` | Metadata storage directory |

---

## Running Without Docker

If you prefer running directly:

```bash
# Python 3.10+ required
pip install -r requirements.txt

# Build frontend (Node.js 18+ required, one-time)
cd frontend && npm install && npm run build && cd ..

# Run
python server.py
```

The app will use `./photos/` as input and `./organized_photos/` as output by default.

---

## Uploading to Synology NAS

After organizing your photos:

1. Open your Synology's **File Station** or map a network drive
2. Copy the contents of `./output/` to your desired photo directory
3. Each folder is already named `City_YYYYMMDD` — ready for browsing

You can also use `rsync` or any SMB/SFTP client to transfer the files.
