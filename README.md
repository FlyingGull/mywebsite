# mywebsite

Personal portfolio website — bilingual (EN/FR), static HTML/CSS, hosted on GitHub Pages.

**Live:** [flyinggull.github.io/mywebsite](https://flyinggull.github.io/mywebsite/)

## Structure

```
index.html          English version
index-fr.html       French version
styles.css          Shared stylesheet
learning-ai.png     Image: AI exploration
electrical-engineering.png  Image: engineering background
values.png          Image: professional values
```

## Preview locally

Open either file directly in a browser:

```bash
open index.html        # macOS
xdg-open index.html    # Linux
start index.html       # Windows
```

Or use any static server:

```bash
python3 -m http.server 8000
# → http://localhost:8000
```

## Deploy

GitHub Pages serves from the root of the `main` branch.

1. Push to `main`
2. Settings → Pages → Source: **Deploy from a branch** → Branch: `main` → `/ (root)`
3. Site publishes automatically

## License

This is a personal portfolio site. All rights reserved unless stated otherwise.
