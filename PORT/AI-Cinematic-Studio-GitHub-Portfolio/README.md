# AI Cinematic Studio 🎬

**AI filmmaking workflow | Portfolio case study | Concept / system design**

A proposed **local-first AI film production workspace** for turning a story into consistent, scene-by-scene AI video production. Designed around screenplay planning, reusable character references, storyboard management, optional GPU rendering in **Google Colab Pro**, and video assembly.

> **Project status — Concept & architecture.** The website in this repository is a **static, illustrative portfolio**. It is *not* a deployed video-generation app. The described AI modules, model integrations, render automation and continuity engine are planned features, not completed implementations.

## 🔗 Live portfolio

After you enable GitHub Pages, your project will be available at your Pages URL. See [Deployment](#deployment-github-pages).

## 🎯 Problem

AI filmmakers often manage scripts, prompts, image references, voiceovers, generated scenes, and edits in disconnected tools. This makes shot continuity, character consistency, file versions, and rendering cost difficult to control.

## 💡 Proposed solution

A single studio interface that makes the workflow explicit:

`Story idea → Screenplay → Character Bible → Storyboard → Keyframes → Video render → Audio & assembly`

### Planned modules

| Module | Planned purpose |
|---|---|
| AI Screenwriter | Generate story outlines, dialogue, scenes and episode structure |
| Character Bible | Store character traits, wardrobe and reference images |
| Pro Director | Guide shot size, camera movement, lighting and visual language |
| Keyframe Studio | Organize reference and keyframe images |
| Scene Editor | Edit scene prompts individually |
| Voice & Sound Studio | Prepare Thai narration, sound design and music cues |
| Film Storyboard | Inspect sequences and shot-level timing |
| Genre Library | Reusable genre and style presets |
| Project Manager | Persist projects and versions |
| Continuity Engine | Flag mismatches in characters, props, place and timeline |

## 🛠️ Proposed technology stack

| Layer | Technology | Status |
|---|---|---|
| Portfolio website | HTML, CSS, minimal JavaScript | **Implemented in this repository** |
| Studio UI | Next.js / React / Tailwind CSS | Planned |
| Backend | Python / FastAPI | Planned |
| Database | SQLite for MVP | Planned |
| Image workflows | ComfyUI | Integration planned |
| Video generation | Wan model family | Experiment planned |
| GPU compute | Google Colab Pro (session-based) | Target environment |
| Asset storage | Google Drive / local files | Planned |
| Video assembly | FFmpeg | Planned |

**Important constraints:** Colab GPU availability and runtime duration vary. Model compatibility depends on available VRAM and workflow configuration. Generated faces and scene continuity are *not* guaranteed by prompting alone. Some hosted video models require paid APIs.

## 🗺️ Roadmap

- **Phase 1 · MVP:** project creation, screenplay, Character Bible, scene editor, storyboard and SQLite persistence.
- **Phase 2 · Models:** connect image workflows, test Wan in Colab, track job state and outputs, and compare render configurations.
- **Phase 3 · Film production:** Thai text-to-speech, subtitles, FFmpeg assembly and continuity checks.

More detail: [Roadmap](docs/roadmap.md) · [Architecture](docs/architecture.md) · [Colab experiment plan](docs/colab-experiment.md)

## 👀 Preview

Open **`index.html`** in any modern browser. The site is responsive and uses CSS-generated storyboard illustrations, so it has no required image assets. Google Fonts are optional; local fallback fonts are provided.

## 🚀 Deployment (GitHub Pages)

1. Create a public repository, for example `ai-cinematic-studio-portfolio`.
2. Upload all files from this folder to the repository root.
3. In the repository, go to **Settings → Pages**.
4. Under **Build and deployment**, choose **Deploy from a branch**; select your default branch and **/(root)**, then save.
5. Open the GitHub Pages URL displayed in Settings → Pages after publishing.

**Tip:** The header's “GitHub ↗” link initially points to `github.com`. After creating your repository, replace it in `index.html` with your real repository URL.

## 📁 Repository structure

```text
.
├── index.html                 # Static portfolio page
├── styles.css                 # Responsive dark cinematic design
├── script.js                  # Dynamic copyright year
├── README.md                  # Portfolio case study
├── docs/
│   ├── architecture.md        # Proposed system design
│   ├── roadmap.md             # Milestones and acceptance criteria
│   └── colab-experiment.md    # Safe first experiment plan
├── .gitignore
└── LICENSE
```

## Next implementation milestone

Build the **Phase 1 data model and a minimal scene-editor prototype**. Track experiments, screenshots and commit history as the project evolves; update the status above only after each capability is working.

## License

Source code and written documentation in this portfolio: [MIT](LICENSE). The license does not apply to third-party AI models or datasets; check their own terms before integrating them.
