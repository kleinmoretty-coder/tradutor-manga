# Manga & Webcomic Translator

![Development Status](https://img.shields.io/badge/status-active_development-orange?style=flat-square)
![Manifest V3](https://img.shields.io/badge/manifest-v3-blue?style=flat-square)
![JavaScript](https://img.shields.io/badge/vanilla-JavaScript-F7DF1E?style=flat-square&logo=javascript&logoColor=black)
[![MIT License](https://img.shields.io/badge/license-MIT-green?style=flat-square)](LICENSE)

A browser extension designed to translate manga and webcomics directly on their original websites, without requiring users to download individual chapters.

The project explores browser automation, image processing, optical character recognition (OCR), persistent storage, and dynamic user interfaces using native JavaScript.

Image acquisition and preprocessing are performed locally. The current OCR implementation uses Azure Computer Vision as an experimental external service, with a fully local solution planned for future development.

> **Development status:** This is an experimental project undergoing architectural improvements. Several components have been implemented and tested during earlier development stages, but the complete translation workflow has not been revalidated in the current version. It should not yet be considered a production-ready extension.

---

## Preview

<p align="center">
  <img width="280" src="https://github.com/user-attachments/assets/07cd416d-5a2d-4621-80d4-015702da5284" alt="Initial language configuration" />
  <img width="280" src="https://github.com/user-attachments/assets/0428ac2c-f52f-4f82-b249-5ac7af444ee4" alt="Active translation session interface" />
</p>

<p align="center">
  <sub>Left: automatic language detection with user confirmation. Right: session management interface.</sub>
</p>

## Project Goals

The main goal is to create a browser extension capable of handling the translation process while preserving the original reading experience.

The intended workflow consists of:

1. Detecting the current chapter and its source language.
2. Identifying and collecting chapter images.
3. Preparing images for OCR while managing memory and request limits.
4. Extracting text and its associated positions.
5. Translating the extracted content.
6. Displaying translated text directly over the original images.
7. Caching processed chapters to avoid unnecessary repeated work.

The project also serves as an opportunity to explore browser APIs, asynchronous programming, modular architecture, and image processing.

## Implemented Components

The repository contains implementations of the following components. Their presence does not necessarily mean every component is fully integrated or working in the current build.

### Language Detection

Detects the chapter's source language and provides an interface for users to confirm or adjust the configuration.

### Image Acquisition and Processing

Implements image collection and preprocessing using browser APIs.

The processing logic includes:

- Image acquisition with fallback strategies.
- Canvas-based processing.
- Image composition and JPEG generation.
- Memory management for image resources.
- Preparation of image batches according to size and dimension constraints.

These mechanisms are intended to reduce unnecessary API requests and support processing larger chapters.

### OCR Integration

Contains an experimental integration with Azure Computer Vision.

The implementation includes:

- Sending image data to the OCR service.
- Polling for asynchronous recognition results.
- Handling unsuccessful requests.
- Retry logic and basic rate-limit handling.
- Preparing image batches for processing.

**Current limitation:** The OCR integration requires further validation. The existing implementation does not yet provide a fully verified, end-to-end processing workflow for all chapter batches.

### Chapter Navigation Monitoring

Includes logic for detecting navigation changes in websites that use Single Page Application (SPA) behavior.

This is particularly relevant to readers that switch chapters without triggering a traditional page reload.

### Dynamic User Interface

Uses dynamically loaded modules and injected interface components to avoid unnecessarily loading interface elements into the host page.

The interface is responsible for presenting configuration options and managing interactions with the reader.

### Persistent Storage

Includes storage management using IndexedDB and Chrome's local storage APIs.

These components are intended to preserve configuration data and previously processed chapter information, supporting future caching and recovery mechanisms.

---

## Architecture

The extension is built using **Chrome Extension Manifest V3** and native JavaScript.

Its main responsibilities are divided across several components.

| Component | Responsibility |
|---|---|
| `content.js` | Interacts with the webpage and coordinates page-related behavior |
| `background.js` | Background event handling, messaging, and extension operations |
| `filtro.js` | Image acquisition and preprocessing |
| `ocr_service.js` | OCR requests, image batching, and recognition processing |
| `storageManager.js` | Persistent storage and data management |
| `avisoManager.js` | Dynamic interface and user notifications |
| `injetor.js` | Interface and module injection |
| `modules/urlMonitor.js` | Monitoring navigation and URL changes |
| `config.js` | Experimental OCR service configuration |

### Processing Flow

The intended processing flow is:

```text
Webpage / Chapter
       |
       v
Content Script
       |
       v
Chapter & Image Detection
       |
       v
Image Acquisition
       |
       v
Image Preprocessing
       |
       v
Batch Generation
       |
       v
OCR Processing
       |
       v
Text Extraction & Positioning
       |
       v
Translation
       |
       v
Page Overlay
       |
       v
Persistent Cache
```

This diagram represents the intended architecture, **not a fully verified execution path in the current version**.

Some components already exist independently, while their integration is still being reviewed.

### Technical Approach

The project deliberately avoids frameworks and build tools at this stage.

It uses native browser APIs and JavaScript modules to explore the underlying mechanisms of browser extensions.

This approach has provided experience with:

- Asynchronous JavaScript and Promises.
- Browser extension messaging and lifecycle management.
- Service workers and content scripts.
- Dynamic module loading.
- Canvas-based image manipulation.
- IndexedDB and persistent data.
- External API integration.
- Error handling and retry strategies.

---

## Tech Stack

| Category | Technology |
|---|---|
| Language | JavaScript (ES2022+) |
| Extension Runtime | Chrome Extension Manifest V3 |
| Image Processing | Canvas API, OffscreenCanvas, ImageBitmap |
| Storage | IndexedDB, `chrome.storage.local` |
| OCR | Azure Computer Vision API (experimental) |
| Architecture | Native JavaScript modules and browser APIs |
| Build Tools | None currently required |

The long-term direction is to replace the external OCR dependency with a local recognition engine and improve the extension's offline capabilities.

---

## Installation

> **Experimental installation:** The current repository is intended primarily for development and testing. Some components may require configuration or debugging before they can be used successfully.

### Requirements

- A Chromium-based browser, such as Chrome, Edge, Brave, Opera, or Vivaldi.
- Git, or a downloaded copy of the repository.
- An Azure Computer Vision resource and valid credentials if testing the external OCR integration.

### 1. Clone the Repository

```bash
git clone https://github.com/kleinmoretty-coder/tradutor-manga.git
cd tradutor-manga
```

### 2. Review OCR Configuration

The project currently contains an experimental `config.js` file used for Azure OCR settings.

To test this integration, review the configuration and provide valid credentials for your own OCR resource.

**Do not commit personal API keys or production credentials to a public repository.**

The configuration mechanism is still experimental and will be reviewed as part of the planned architectural improvements.

### 3. Load the Extension

1. Open `chrome://extensions/`.
2. Enable **Developer mode**.
3. Select **Load unpacked**.
4. Choose the cloned project directory.

The browser should recognize the extension through its `manifest.json` file.

Successful installation does not guarantee that the complete translation pipeline will work in the current build.

---

## Usage

The intended user interaction is:

1. Open a manga chapter on a supported reader.
2. Click the extension icon.
3. Confirm or adjust the detected source language.
4. Select the desired target language.
5. Start the translation session.

The extension is designed to monitor chapter navigation and manage the translation workflow without requiring the user to manually download images.

**Important:** This workflow describes the intended user experience. Full OCR-to-translation-to-overlay functionality remains subject to integration and regression testing.

---

## Known Limitations & Technical Debt

The project was developed incrementally, with individual components implemented and tested as new requirements emerged.

As a result, the current repository contains both working implementations from earlier development stages and unfinished integration work.

The main known areas for improvement are described below.

### 1. Incomplete End-to-End Integration

Image preprocessing and OCR communication have been implemented, but the complete workflow connecting image acquisition, OCR, translation, and on-page rendering still requires validation.

The immediate priority is to restore and test the existing processing flow before introducing major new features.

### 2. OCR Batch Orchestration

The code includes image batching and Azure OCR request handling.

However, processing all batches through a coordinated queue is not fully implemented in the current version.

This requires additional work on request scheduling, error recovery, and result aggregation.

### 3. Architectural Organization

Some modules currently handle multiple responsibilities or contain experimental logic alongside production-oriented code.

A future refactoring phase will focus on:

- Establishing clearer module boundaries.
- Reducing unnecessary coupling.
- Separating processing logic from interface management.
- Improving error propagation and handling.
- Making individual components easier to test.
- Applying object-oriented design where it provides practical benefits.

The objective is not to introduce unnecessary abstraction, but to improve maintainability, readability, and extensibility.

### 4. Configuration and Permissions

The current configuration approach was created for experimentation.

It requires improvements to credential handling, environment configuration, and separation between development settings and distributable extension code.

Extension permissions will also be reviewed to reduce unnecessary access and better follow the principle of least privilege.

### 5. Browser and Website Compatibility

The project has primarily been developed around Chromium-based browsers and manga reading workflows.

Different websites may use distinct image delivery mechanisms, navigation patterns, and security restrictions.

Compatibility beyond the original development environment still requires testing and dedicated handling.

### 6. Testing and Reliability

Some features were implemented and tested in previous iterations but have not been systematically revalidated after later changes.

Future work will include regression testing, isolated component tests, and more consistent debugging and error reporting.

---

## Roadmap

The roadmap reflects the current development direction and may evolve as the architecture is reviewed.

### Existing Foundations

- [x] Automatic language detection
- [x] Dynamically injected user interface
- [x] Persistent storage implementation
- [x] Dynamic module loading
- [x] Image acquisition and preprocessing
- [x] Chapter navigation monitoring
- [x] Experimental Azure OCR integration

### Integration & Reliability

- [ ] Revalidate previously implemented functionality
- [ ] Restore and verify the end-to-end OCR workflow
- [ ] Complete multi-batch processing and queue management
- [ ] Improve OCR result aggregation and error recovery
- [ ] Complete and validate on-page translation overlays
- [ ] Add regression tests for critical components

### Architecture & Maintainability

- [ ] Refactor modules with clearer responsibilities
- [ ] Improve separation of concerns
- [ ] Review extension permissions and credential handling
- [ ] Introduce consistent error handling and diagnostics
- [ ] Improve documentation and development workflows

### Future Development

- [ ] Replace Azure OCR with a local OCR engine
- [ ] Improve offline functionality
- [ ] Expand compatibility with additional manga and webcomic websites
- [ ] Optimize image processing and memory usage

---

## Development Philosophy

This project is both a practical software experiment and a long-term learning project.

Its development has involved exploring browser internals, solving image processing problems, integrating external services, and designing communication between extension components.

Some early architectural decisions are expected to change as the project matures.

Rather than rewriting the entire application without a clear reason, future development will prioritize understanding existing behavior, restoring integration, and progressively improving the architecture.

The goal is to build a reliable, maintainable extension while preserving the technical knowledge gained throughout its development.

---

## Contributing

Issues, bug reports, and technical suggestions are welcome.

Since the project is undergoing architectural changes, opening an issue before proposing major modifications is recommended.

Contributions related to testing, documentation, image processing, OCR integration, and architectural improvements are especially relevant.

---

## License

This project is licensed under the [MIT License](LICENSE).
