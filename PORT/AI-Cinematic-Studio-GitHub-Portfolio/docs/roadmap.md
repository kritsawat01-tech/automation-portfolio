# Implementation Roadmap

This is a **proposed plan**, not a record of completed milestones.

## Phase 1 — Local-first planning MVP

**Outcome:** A developer can create a film project and plan its story in a local web app without invoking a GPU.

- [ ] Set up Next.js frontend and FastAPI backend.
- [ ] Create SQLite tables for projects, episodes, characters and scenes.
- [ ] Add create/edit/archive project operations.
- [ ] Build screenplay and character reference forms.
- [ ] Add scene ordering and storyboard timeline.
- [ ] Export/import versioned project JSON.
- [ ] Write unit tests for data validation and ordering.

**Acceptance criteria:** Projects persist across restarts; scene order is deterministic; saved character references are linked to the correct project; exports can be restored.

## Phase 2 — Image and video model experiments

**Outcome:** A creator can prepare a manifest, run a supported generation workflow and re-import results.

- [ ] Build model-provider interface.
- [ ] Connect optional ComfyUI image generation.
- [ ] Create a Colab notebook prototype for one supported Wan configuration.
- [ ] Save GPU details, selected settings and generated MP4 output metadata.
- [ ] Implement job states: `draft`, `queued`, `running`, `succeeded`, `failed`, `approved`.
- [ ] Add asset versioning and retry support.

**Acceptance criteria:** At least one verified end-to-end clip is generated and linked to a scene; a disconnected notebook does not silently erase completed output.

## Phase 3 — Assisted film assembly

**Outcome:** Approved scenes can be assembled into a short-form video with audio.

- [ ] Thai narration pipeline and audio timing.
- [ ] Subtitle generation and review.
- [ ] Assemble scenes with FFmpeg to MP4.
- [ ] Continuity checks across scenes.
- [ ] Export vertical 9:16 and landscape 16:9 presets.
- [ ] Add budget and throughput reports.

**Acceptance criteria:** A reviewed multi-scene export is created with synchronized narration and subtitles; a human can approve/reject each generated scene.

## Portfolio evidence to add as development progresses

- Screenshots of actual running UI (replace the illustrative mockup only when available).
- Dated Colab GPU configurations and settings, including failures and successful test renders.
- Verified costs or measured GPU usage for tested workloads.
- Test coverage and performance notes.
- Short demo clip and walkthrough when permission to share assets is confirmed.
