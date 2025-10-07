# Orange Logic Technical Writing Demo

This repository contains a short documentation demo prepared for the **Orange Logic Technical Writing Assessment (October 7 2025)**.  
It showcases technical writing best practices using **MkDocs** and **GitHub Pages** for deployment.

---

## 📘 Overview

The demo site presents rewritten and polished training material for two example features of a Digital Asset Management (DAM) system:

- **Auto Metadata Mapping** – how metadata is automatically applied to uploaded assets.  
- **Workflow Automation** – how the system triggers automated tasks when metadata is updated.

Each page demonstrates clear structure, concise explanations, and consistent tone — modeled after internal training documentation.

---

## 🧱 Tech Stack

- [MkDocs](https://www.mkdocs.org/) — static site generator for documentation  
- [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) — clean, responsive theme  
- [GitHub Pages](https://pages.github.com/) — hosting and deployment  

---

## 🚀 Deployment

The site is automatically deployed through GitHub Actions to the `gh-pages` branch.  
You can rebuild and redeploy manually by running:

```bash
mkdocs gh-deploy --force
