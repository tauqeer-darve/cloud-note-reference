# Note Image Library

A single-file web app for storing images and PDFs and referencing them by ID in handwritten notes (e.g. `IMG-001`, `PDF-001`).

The app is one `index.html` hosted on GitHub Pages. Your files and metadata live in a **separate private GitHub repository**, accessed through the GitHub REST API. There is no backend, database, or build step.

## Setup

### 1. Create the private repository

Create an empty **private** repo (e.g. `note-library`). Add at least one commit (a README is fine) so the branch exists.

### 2. Create a fine-grained token

GitHub → Settings → Developer settings → Personal access tokens → Fine-grained tokens:

- **Repository access:** Only select repositories → your private repo
- **Permissions:** Repository → **Contents: Read and write** (nothing else)

### 3. Host the app

Put `index.html` in a **public** repo and enable GitHub Pages (Settings → Pages → deploy from branch). Open the Pages URL.

### 4. Connect

Enter your GitHub username, the private repo name, the branch, and the token. Tick **Remember credentials** to stay signed in on this browser.

## Usage

- **Upload:** choose a file, drag and drop, or take a photo. Pick an existing topic from the dropdown or choose "Create new topic". Add a description and upload.
- **Gallery:** filter by type (Images / PDFs) and topic, or search by ID, topic, or description.
- **Copy ID / Copy Markdown:** copies the ID (e.g. `IMG-001`) for use in your notes.
- **PDFs:** tap the tile or **View** to open the built-in viewer (page navigation, zoom, open in new tab, download).
- **Edit:** change topic or description. Changing the topic moves the file to the new topic folder; the ID never changes.
- **Delete:** asks for confirmation, removes the file and its metadata. The ID is never reused.

Supported files: JPG, JPEG, PNG, WEBP, PDF (max 20 MB).

## Repository layout (private repo)

```
index.json
image/
  AWS/IMG-001.jpg
  Linux/IMG-002.png
pdf/
  AWS/PDF-001.pdf
```

- IDs are global across topics. Images use `IMG-###`, PDFs use `PDF-###` (separate sequences).
- `index.json` stores the metadata and counters that keep deleted IDs retired.
- Topic names are sanitized (letters, numbers, spaces, `-`, `_`) and matched case-insensitively, so `aws` and `AWS` are the same topic.
- Files from older versions (e.g. `AWS/IMG-001.jpg` or `images/…`) keep working and move into `image/` when you change their topic.

## Security notes

- The token is never in the source code. It is only stored in `localStorage` if you tick "Remember credentials", and it is never displayed again after entry.
- Anyone with access to your browser profile may be able to read the stored token. Use a token limited to the one private repo, and use **Clear Credentials** (Settings) on shared devices.
- If you host other sites on the same `username.github.io`, they share the same browser storage origin. Use a dedicated repo or custom domain for this app.
- Deleted files remain in the private repo's git history.

## Limitations

- Images and PDFs are downloaded through the API on each visit (no offline cache or thumbnails).
- No automatic resizing or compression; large files grow the repo quickly.
- The PDF viewer loads PDF.js from cdnjs the first time you open a PDF.
- HEIC photos are not supported.
