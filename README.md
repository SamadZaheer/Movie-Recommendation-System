# Movie Recommendation System

Content-based movie recommendation engine using NLP and cosine similarity on the TMDB 5,000 dataset, with exploratory collaborative filtering via SVD matrix factorisation.

---

## Project Overview

Recommendation engines power Netflix, Spotify, and Amazon — but how do they actually work? This project builds a content-based movie recommender from scratch, encoding each film as a feature vector derived from its genres, cast, director, and plot keywords, then using cosine similarity to surface the most thematically related titles.

The system correctly identifies thematic relationships between films: querying "Avatar" returns Guardians of the Galaxy, Star Trek, and John Carter — demonstrating that the model has learned genuine semantic relationships, not just surface-level genre matches.

Collaborative filtering via SVD matrix factorisation is also explored as a complementary approach.

---

## Dataset

| File | Source | Description |
|------|--------|-------------|
| `tmdb_5000_movies.csv` | TMDB / Kaggle | Metadata for 5,000 films: genres, keywords, overview, runtime, vote data |
| `tmdb_5000_credits.csv` | TMDB / Kaggle | Cast and crew information per film |
| `ratings_small.csv` | MovieLens | User ratings (used for exploratory collaborative filtering only) |

---

## Approach

1. **Data loading & merging** — merged `tmdb_5000_movies.csv` and `tmdb_5000_credits.csv` on movie ID
2. **Preprocessing** — checked for missing values; removed null entries; converted JSON-like string columns (`genres`, `keywords`, `cast`, `crew`) into Python objects using `ast.literal_eval`
3. **Feature engineering** — extracted top 3 cast members and director; combined genres, keywords, cast, director, and plot overview into a single `tags` column per film; lowercased and stripped spaces for consistent vectorisation

<p align="center">
  <img src="images/genre_distribution.png" alt="Genre Distribution" width="600"/>
</p>

4. **Text vectorisation** — applied `CountVectorizer` with `max_features=5000` and English stop words to convert `tags` into sparse feature vectors
5. **Cosine similarity** — computed a full similarity matrix across all 5,000 films; cosine similarity measures the angle between vectors (1 = identical, 0 = unrelated)
6. **Recommendation function** — finds the queried film by title, retrieves its similarity scores, sorts all films by score descending, returns the top 10 most similar titles

```python
get_recommendations("Avatar")
# → Guardians of the Galaxy, Star Trek, John Carter, Aliens vs Predator, Titan A.E., ...
```

<p align="center">
  <img src="images/recommendation_example.png" alt="Recommendation Example" width="800"/>
</p>

7. **Exploratory collaborative filtering** — implemented SVD matrix factorisation using the `Surprise` library on `ratings_small.csv`; evaluated with RMSE and MAE; explored as a complementary personalisation approach (not integrated into the final recommender)

<p align="center">
  <img src="images/workflow.png" alt="Project Workflow" width="700"/>
</p>

---

## Key Findings

- **Content-based filtering successfully captures thematic relationships** — "Avatar" → Guardians of the Galaxy, Star Trek; the system has learned genre, cast, and narrative similarities
- **Director and cast are the most discriminating features** for consistent genre-level recommendations; plot keywords add specificity
- **SVD collaborative filtering requires dense rating matrices** to perform well — the sparse user-film matrix in `ratings_small.csv` leads to cold-start limitations for less-rated films
- **Content-based approaches scale well** and require no user history, making them well-suited for new platform users or cold-start scenarios
- **Combining content signals with collaborative signals** (hybrid systems) would yield more personalised results — content-based provides a strong, interpretable baseline

**Content-based vs Collaborative — key differences:**

| Approach | Data Required | Personalisation | Cold Start |
|----------|--------------|-----------------|------------|
| Content-based (this project) | Film metadata | None (same for all users) | Handles well |
| Collaborative (SVD, exploratory) | User rating history | Yes | Struggles |

---

## How to Run

```bash
git clone https://github.com/SamadZaheer/Movie-Recommendation-System.git
cd Movie-Recommendation-System
pip install -r requirements.txt
jupyter notebook "Movie Recommendation System.ipynb"
```

> The TMDB dataset files (`tmdb_5000_movies.csv`, `tmdb_5000_credits.csv`) are included in the repository. `ratings_small.csv` is used only for the exploratory collaborative filtering section.

---

## Author

**Samad Zaheer** — Master of Information Technology (Data Science), Queensland University of Technology (QUT)
