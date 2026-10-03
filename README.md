# MovieLens Movie Recommender

An item-based collaborative filtering project that recommends movies using similarities in user-rating patterns.

[View the analysis notebook](movielens-movie-recommender.ipynb)

## Project Overview

This project explores how movie ratings can be used to generate recommendations. Given a movie title, the recommendation function returns ten movies with the most similar rating vectors, ranked by cosine similarity.

The notebook covers data inspection, exploratory analysis, matrix preparation, similarity calculation, and example recommendations. It also examines limitations involving sparse ratings, popularity bias, and movies without rating histories.

## Dataset

The project uses the MovieLens Latest Small dataset from GroupLens Research.

| Dataset characteristic | Count |
|---|---:|
| User ratings | 100,836 |
| Users | 610 |
| Movies in the catalog | 9,742 |
| Movies with ratings | 9,724 |
| Movies without ratings | 18 |

Download the dataset from the [GroupLens MovieLens page](https://grouplens.org/datasets/movielens/latest/). Select the **small dataset**, `ml-latest-small.zip`.

The notebook loads four files:

- `movies.csv`: movie titles and genres
- `ratings.csv`: user ratings and timestamps
- `tags.csv`: user-generated tags
- `links.csv`: external movie database identifiers

Recommendations use the rating data. Titles and genres are added to the results for readability; tags and external identifiers are inspected but do not determine similarity.

## Approach

1. **Inspect the data:** Check file structures, missing values, duplicate records, and identifier uniqueness.
2. **Explore rating patterns:** Compare rating counts and average ratings, including movies with limited feedback.
3. **Build a movie-user matrix:** Represent movies as rows and users as columns.
4. **Prepare sparse storage:** Fill missing entries with zero and convert the matrix to compressed sparse row (CSR) format. Zero represents an unobserved rating, not an explicit dislike.
5. **Calculate similarity:** Use cosine similarity to compare movie-rating vectors.
6. **Generate recommendations:** Rank similar movies, exclude the input movie, and return titles, genres, and similarity scores.

## Example Usage

After running the notebook’s setup and model-building cells:

```python
recommend_movies("Forrest Gump (1994)")
```

Request a different number of recommendations:

```python
recommend_movies("Toy Story (1995)", n=5)
```

Enter the title exactly as it appears in the dataset, including capitalization and year.

The function returns a ranked DataFrame containing:

- Movie ID
- Cosine similarity
- Movie title
- Genres

It returns an explanatory message when the title is not found or the movie has no ratings.

## Findings

- The movie-user matrix contains **9,724 movies and 610 users**.
- Only **1.70%** of possible movie-user combinations have ratings; the matrix is **98.30% sparse**.
- **Toy Story 2 (1999)** was the highest-ranked recommendation for **Toy Story (1995)**.
- **Chalet Girl (2011)** appears in the catalog but has no ratings, demonstrating the item cold-start problem.
- Some movies have perfect average ratings based on only one or two observations. Rating counts provide important context when interpreting those averages.

The example recommendations demonstrate the function’s behavior. No held-out evaluation or user study was performed, so recommendation accuracy has not been established.

## Tools

- Python
- pandas
- NumPy
- SciPy
- scikit-learn
- JupyterLab

## How to Run

1. Download or clone this repository.
2. Download and extract `ml-latest-small.zip` from GroupLens.
3. Place all four CSV files in the same folder as `movielens-movie-recommender.ipynb`.
4. Install the required packages:

   ```bash
   python -m pip install pandas numpy scipy scikit-learn jupyterlab
   ```

5. Open the notebook in JupyterLab and run the cells in order.
6. Call `recommend_movies()` with an exact movie title.

Saved notebook outputs can be reviewed without executing the code.

## Limitations

### Similarity is not accuracy

Cosine similarity measures alignment between rating vectors. A score of 0.60 does not mean that a recommendation is 60% accurate or that a user has a 60% chance of enjoying it.

### Limited personalization

Recommendations depend on a single input movie rather than a user’s complete rating history. The same input title produces the same recommendations, and previously watched movies are not filtered out.

### Sparse data and cold start

Most movie-user combinations have no rating. Limited overlap between users can weaken similarity estimates, and movies without ratings cannot be used to generate recommendations.

### Popularity and rating behavior

Popular movies have more rating evidence and may be favored by the approach. The implementation also uses raw ratings without adjusting for differences in how generously individual users rate movies.

### Computational scalability

Although the ratings use sparse storage, the similarity calculation creates a dense **9,724 × 9,724** matrix. Storing similarities for every movie pair becomes increasingly expensive as the catalog grows.

### Exact-title matching

The function requires an exact title match. Partial titles, spelling differences, or omitted years are not resolved automatically.

## Future Improvements

- Evaluate ranking quality using held-out interactions and metrics such as precision@k and recall@k.
- Compare recommendations with a popularity-based baseline.
- Combine rating patterns with genres or tags to support movies with limited rating histories.
- Add partial-title search and disambiguation.
- Calculate similarities on demand or retain only the nearest neighbors.
- Incorporate user history and filter out previously rated movies.

## Key Takeaway

Recommendation systems depend on both preference patterns and the amount of evidence behind them. This project demonstrates a working collaborative filtering approach while showing why sparse data, popularity, and evaluation design matter when interpreting its recommendations.

## References

GroupLens Research. (2018). *MovieLens latest datasets*. University of Minnesota. https://grouplens.org/datasets/movielens/latest/

Harper, F. M., & Konstan, J. A. (2015). The MovieLens datasets: History and context. *ACM Transactions on Interactive Intelligent Systems, 5*(4), Article 19. https://doi.org/10.1145/2827872

Sarwar, B., Karypis, G., Konstan, J., & Riedl, J. (2001). Item-based collaborative filtering recommendation algorithms. In *Proceedings of the 10th International Conference on World Wide Web* (pp. 285–295). Association for Computing Machinery. https://doi.org/10.1145/371920.372071

## Author

Stephanie Nord
