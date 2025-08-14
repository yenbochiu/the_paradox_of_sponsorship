# The Paradox of Sponsorship — YouTube Sponsored Content & Engagement (2022–2024)

> End‑to‑end, reproducible research project analysing how **sponsored** YouTube videos affect **creator credibility** and **audience engagement**.  
> Built with Python (pandas/statsmodels/OpenCV/VADER), YouTube Data API + scraping (yt‑dlp).

**Repository highlights**
- 📄 **Paper**: `docs/The Paradox of Sponsorship.pdf` (methods + results)
- 📓 **Notebook**: `notebooks/main_code.ipynb` (data build, feature engineering, modelling, figs)
- 🧰 **Tech**: Python 3.10+, pandas, statsmodels, scikit‑learn, OpenCV, VADER, google‑api‑python‑client, yt‑dlp
- 🔐 **No raw datasets included** (scraped/large). You get **schema + scripts + instructions** to rebuild locally.
- ✅ **Reproducibility**: pinned `requirements.txt`, `environment.yml`, and minimal config in `config/.env.example`

---

## Elevator pitch

This project studies **18,937 videos across 137 U.S. YouTube channels (People & Blogs / Entertainment, 2022–2024)** to quantify whether sponsorship changes: views, likes, comments, and **comment sentiment**. We use a **within‑channel fixed‑effects** design to control for creator‑specific style/audience; add **thumbnail computer‑vision features** (brightness, saturation, colour temperature, face count); and analyse **textual signals** (title/description).

**Key findings (short):**
- Sponsorship does **not significantly reduce** views/likes/comments within the same channel.
- **Positive comment ratio is slightly higher** for sponsored videos.
- **Micro‑creators** are **more resilient** to sponsorship fatigue (esp. when they sponsor regularly).
- **Visual framing matters**: warm/bright thumbnails can subtly moderate engagement, textual cues less so.

> Full details, figures, and robustness checks are in the paper.

---

## 🇳🇱 Korte samenvatting (voor werkgevers in NL)

Een praktische, end‑to‑end onderzoeksrepo (Python) die laat zien hoe ik **scraping**, **feature‑engineering**, **econometrische modellen** en **ML tooling** combineer. Zonder data in de repo (wegens scraping/ToS), maar met **heldere instructies en config** zodat u lokaal kunt reproduceren. Focus op **causale interpretatie (FE‑regressies)** boven pure voorspelling.

---

## Repository structure

```
.
├── README.md
├── notebooks/
│   └── main_code.ipynb          # Provided by author (analysis & figures)
└── docs/
    └── The Paradox of Sponsorship.pdf
```

> **Note**: Place your copies of the two files into `notebooks/` and `docs/` before committing.

---

## Getting started

### 1) Environment

- Python **3.10+** (3.11 works)
- OS packages: `ffmpeg` (yt‑dlp post‑processing), system OpenCV deps (Linux: `libgl1`, `libglib2.0-0`)

```bash
# Option A: pip
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt

# Option B: conda/mamba
mamba env create -f environment.yml
mamba activate sponsorship
```

### 2) Configure secrets & paths

Create your local `.env` from the template and fill in **your** keys (do **not** commit `.env`):

```bash
cp config/.env.example config/.env
```

`config/.env`

```
# YouTube Data API v3
YOUTUBE_API_KEY=YOUR_API_KEY

# Optional: path overrides
DATA_DIR=./data
CACHE_DIR=./.cache
```

The notebook expects a YouTube API key to fetch top comments and may call `yt‑dlp` to fetch public metadata/thumbnails.

### 3) (Optional) Rebuild data locally

Because the full scraped dataset is not included, the notebook is structured in independent blocks:

1. **Channel list**: build a CSV of seed channels (e.g., via SocialBlade or ChannelCrawler exports).
2. **Video metadata**: use `yt-dlp` to fetch `video_id`, title, upload date, counts, description, thumbnail.
   ```bash
   yt-dlp --dump-json --flat-playlist "https://www.youtube.com/@<channel>" > channel.jsonl
   ```
3. **Comments (sample)**: use YouTube Data API v3 and your `YOUTUBE_API_KEY` to fetch up to 50 top comments/video.
4. **Thumbnail features**: OpenCV pipeline (HSV brightness/saturation, warm/cool temperature proxy, face count).
5. **Preprocessing**: log transforms, IQR trimming, standardisation.
6. **Modelling**: channel fixed‑effects OLS (statsmodels), clustered SE at channel level; interaction terms for moderators.
7. **Robustness**: leave‑one‑channel‑out, alt specs.

> Tip: you can point paths to your own **small sample** to run end‑to‑end without the full scrape.

---

## How to run the notebook

1. Open `notebooks/main_code.ipynb`.
2. Set paths (data, output) near the top; ensure your API key is pulled from `os.getenv("YOUTUBE_API_KEY")` rather than hard‑coding.
3. Execute cells section‑by‑section (data build → features → models → figures).

### Repro outputs
- Tables/figures are generated in‑notebook; export to `docs/figures/` if you want to include them in the repo.

---

## Methods (at a glance)

- **Design**: Within‑channel panel with **channel fixed effects** to remove time‑invariant creator heterogeneity.
- **Outcomes**: log(view), log(like), log(comment), log(positive‑comment‑ratio + ε).
- **Features**: sponsored flag from description keywords; thumbnail **brightness/saturation/temperature/face_count** (OpenCV); title/description length; channel size tiers; % sponsored per channel.
- **Stats**: OLS FE via `statsmodels` + **clustered SE by channel**, interactions for moderators; robustness via leave‑one‑out.
- **NLP**: VADER for comment sentiment (up to 50 top comments/video).

See the paper for full details.

---

## Ethics, compliance & data policy

- This repo ships **no raw data**. Rebuilding uses **public** endpoints and follows platform **ToS**.
- Respect creators’ privacy and rate limits; store only necessary metadata; do not attempt to deanonymise users.
- Disclose sponsorships/brand relations transparently when publishing any derivative analysis.

---

## What to review (for hiring managers)

- `notebooks/main_code.ipynb`: clean data pipeline, feature engineering, regression specification, fig quality.
- `docs/The Paradox of Sponsorship.pdf`: problem framing, literature linkage, econometric reasoning, robustness, and stakeholder implications.
- Code demonstrates: **Python**, **statsmodels**, **OpenCV**, **YouTube API**, **scraping (yt‑dlp)**, **experiment design** and **plotting**.

---

## Limitations & future work

- Description‑based sponsorship labelling can misclassify non‑disclosed paid content.
- VADER struggles with sarcasm; consider transformer‑based sentiment models.
- Add watch‑time / CTR if available; expand beyond U.S. and content categories; formal causal identification (event studies).

---

## Citation

If you use this work, please cite the PDF and this repository.

---

## Author

Name: Yen-Bo Chiu    
Contact: yenbochiu@gmail.com    
Edu: MSc Business Analytics, University of Edinburgh/ BA Economics, National Taiwan University  
Loc: I'm currently based in Amsterdam, Netherlands/ Taipei, Taiwan

