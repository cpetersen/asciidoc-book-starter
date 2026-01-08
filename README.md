[![book-generate-pdf](https://github.com/cpetersen/asciidoc-book-starter/actions/workflows/book-generate-pdf.yaml/badge.svg?branch=main)](https://github.com/cpetersen/asciidoc-book-starter/actions/workflows/book-generate-pdf.yaml)

# AsciiDoc Book Starter

This is a template repository for authoring books using AsciiDoc.

I've briefly explored other formats such as Markdown, Latex, and Pandoc but I've found AsciiDoc to be the most flexible and powerful format for authoring books. It is easily readable and writable to a human, has a lax syntax and good set of defaults for authoring books, and it can be easily converted to other formats such as PDF, ePUB and HTML.

AsciiDoc is also a very powerful format for authoring technical documentation, and is widely used in the media and content publishing industry, such as in O'Reilly's books.

## Basics of AsciiDoc and Writing

An important observation to get started when authoring a book with AsciiDoc is the notion of the language vs the implementations. AsciiDoc is a language that's intended to be a lightweight semantic markup. To generate output from AsciiDoc we use text processor tools such as [Asciidoctor](https://asciidoctor.org/), which is free and open source.

Get up to date with the latest AsciiDoc syntax and features by reading the [AsciiDoc User Guide](https://asciidoctor.org/docs/asciidoc-writers-guide/).

## Features

Book authoring experience provides the following features with this repository:
- Table of Contents (TOC) generation.
- Template prelude chapters: A `Preface`, and a `Forward`.
- Template chapters with commonly used formatting in books.
- Chapters are structured into their own chapter directories so they can be co-located with their images and other assets, such as code snippets.
- A PDF output that uses a theme, and can be customized.
- A PDF output that uses custom fonts (Google's open fonts family). Specifically, an [Open Sans](https://fonts.google.com/specimen/Open+Sans) font for the body text, and a [Source Code Pro](https://fonts.google.com/specimen/Source+Code+Pro?query=source+code+pro) font for source code snippets and inline code.

Book generation:
- No need for a local installation of Asciidoctor, as the book generation is done via Docker.
- No need for special CI setup, as the book generation is done via Docker.
- A unified CLI tool (`bin/press`) to generate the book in various formats, including PDF, ePUB, Word, and plain text.

## Getting Started with AsciiDoc Book Starter

We start off by getting familiar with the repository structure and the various files that are part of it.

The top-level directory structure looks like this:

```
.
├── README.md
├── Gemfile
├── bin
│   └── press              # CLI tool for building and managing the book
├── config
│   ├── book.yml           # Book metadata (title, authors, etc.)
│   └── pronunciations.yml # TTS pronunciation overrides
├── book
│   ├── preface.adoc
│   ├── foreword.adoc
│   ├── index.adoc
│   ├── chapter-01-The-Beginning
│   │   └── content.adoc
│   ├── chapter-02-The-Rocket
│   │   └── content.adoc
│   ├── chapter-03-How-Planet-Systems-Work
│   │   ├── content.adoc
│   │   └── images/
│   ├── fonts/
│   ├── images/
│   │   └── icons/         # SVG icons for Word export
│   └── themes/
├── build/                 # Generated output files
└── notes/                 # Research notes and audio recordings
```

The `book` directory is where the book content is stored:
- The `index.adoc` file is the main entry point for the book, and it's where we include all the other chapters and prelude chapters.
- The `images/` directory is where you can store images that are used in the book.
- Chapters are written in their own directory, and each chapter directory contains a `content.adoc` file which is the main entry point for the chapter, and an optional `images` directory for images that are used in the chapter. This helps to colocate assets for the same chapter together rather than having them all mixed together in one big directory.
- In the same directory, you'll find the theme-able PDF `themes` directory, and the `fonts` directory which contains the fonts used in the book.

## Configuration

Book metadata is stored in `config/book.yml`:

```yaml
title: "Your Book Title"
subtitle: "Your Subtitle"
authors:
  - "Author Name"
  - "Co-Author Name"

# Optional: Override the output filename (defaults to lowercase, hyphenated title)
# filename: "my-book"

audiobook:
  genre: "Audiobook"
```

This configuration is used for generating output filenames and embedding metadata in PDF, ePUB, and audiobook files.

## Prerequisites

- **Docker** - Required for PDF, ePUB, and Word generation
- **Ruby** - Required to run the `bin/press` CLI tool
- **Bundler** - Run `bundle install` to install dependencies
- **ffmpeg** - Required for audiobook assembly (optional)
- **ImageMagick or librsvg** - Required for SVG cover conversion (optional)

## The Press CLI Tool

All book operations are handled through the `bin/press` command-line tool. Run `bin/press help` to see all available commands.

### Building Output Formats

#### PDF

Build the book as a PDF file:

```bash
bin/press pdf
```

Output: `build/<book-name>.pdf`

#### ePub

Build the book as an ePub file:

```bash
bin/press epub
```

Output: `build/<book-name>.epub`

#### Word (DOCX)

Export sections to Word format for editing or review:

```bash
# Export all sections
bin/press docx

# Export a specific section
bin/press docx chapter-01

# Specify output directory
bin/press docx --output-dir=build/review
```

Output: `build/docx/<section-name>.docx`

#### Plain Text / Markdown

Export the book to plain text or markdown:

```bash
# Export to plain text
bin/press export txt -o build/book.txt

# Export to markdown
bin/press export markdown -o build/book.md
```

### Book Statistics

Generate word counts and readability statistics:

```bash
# Summary statistics
bin/press stats

# Breakdown by chapter
bin/press stats --by-chapter

# Output as JSON
bin/press stats --json
```

### Working with Word Documents

#### Importing Word Files with Track Changes

Import a Word document and preserve track changes markup:

```bash
# Import with all changes visible
bin/press import_docx manuscript.docx

# Accept all changes during import
bin/press import_docx manuscript.docx --track-changes=accept

# Reject all changes during import
bin/press import_docx manuscript.docx --track-changes=reject

# Output as markdown instead of AsciiDoc
bin/press import_docx manuscript.docx --format=markdown
```

### Audiobook Generation

Generate an audiobook using OpenAI's text-to-speech API. Requires the `OPENAI_API_KEY` environment variable.

```bash
# Preview what would be built (no API calls)
bin/press audiobook --dry-run

# Build all chapters
bin/press audiobook

# Build a specific chapter
bin/press audiobook --chapter=01

# Use a different voice (alloy, echo, fable, nova, onyx, shimmer)
bin/press audiobook --voice=nova

# Force rebuild all chapters
bin/press audiobook --force
```

Output: `build/<book-name>.m4b`

### Voice Note Transcription

Transcribe voice memos from `notes/audio/` using OpenAI Whisper. Requires the `OPENAI_API_KEY` environment variable.

```bash
# Preview files to transcribe
bin/press transcribe --dry-run

# Transcribe all new audio files
bin/press transcribe
```

Transcriptions are saved to `notes/audio-notes.csv` and processed files are moved to `notes/complete-audio/`.

### Cover Image Conversion

Convert SVG cover images to PNG:

```bash
# Default width (1600px)
bin/press covers

# Custom width
bin/press covers --width=2400
```

Converts all `cover*.svg` files in `book/images/` to PNG format.

## AsciiDoc Book Assets

Static assets for the book are stored in the `book` directory, and include the following:
- The `images` directory is where you can store images that are used in the book. Inside this directory is a `cover.jpeg` image used for the book's cover.
- The `fonts` directory is where you can store fonts that are used in the book. It currently houses the [Open Sans](https://fonts.google.com/specimen/Open+Sans) and [Source Code Pro](https://fonts.google.com/specimen/Source+Code+Pro?query=source+code+pro) fonts, both with their original `.zip` file archived as downloaded from the Google Fonts website as well as extracted each to its own directory.

### Icons

The `book/images/icons` directory contains SVG icons used for inline icons (`icon:name[]`) and admonition blocks (`[TIP]`, `[WARNING]`, etc.) when exporting to Word (DOCX) format.

**Attribution:** Icons are from [Font Awesome Free](https://fontawesome.com) and are licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

**Adding new icons:** If you need additional icons, download them from the [Font Awesome GitHub repository](https://github.com/FortAwesome/Font-Awesome/tree/6.x/svgs):
- Solid icons: `svgs/solid/`
- Brand icons: `svgs/brands/`

Save the SVG with the same name you use in your AsciiDoc (e.g., `icon:rocket[]` needs `rocket.svg`).

**Important:** After downloading, add `width="16" height="16"` to the `<svg>` tag. Font Awesome SVGs don't include dimensions by default, which causes them to render very large in Word documents. For example:

```xml
<!-- Before -->
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512">...</svg>

<!-- After -->
<svg width="16" height="16" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512">...</svg>
```

## Author

Liran Tal <liran@lirantal.com>
