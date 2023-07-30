<!DOCTYPE html>
<html>
<head>
  <title>Mini IMDB Clone</title>
  <!-- Link your CSS file -->
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="container">
    <!-- Home Page -->
    <div id="search-container">
      <input type="text" id="search-input" placeholder="Search for a movie...">
    </div>
    <div id="search-results-container"></div>
  </div>

  <script src="script.js"></script>
</body>
</html>
<!DOCTYPE html>
<html>
<head>
  <title>Movie Page</title>
  <!-- Link your CSS file -->
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="container">
    <!-- Movie Page -->
    <div id="movie-page-container">
      <!-- Movie details will be rendered here -->
    </div>
    <div>
      <a href="index.html">Back to Home</a>
    </div>
  </div>

  <script src="script.js"></script>
</body>
</html>
/* General styles */
body {
  font-family: Arial, sans-serif;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

/* Home Page styles */
#search-container {
  text-align: center;
  margin-bottom: 20px;
}

#search-input {
  padding: 10px;
  font-size: 16px;
  width: 100%;
  max-width: 500px;
}

#search-results-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.movie-card {
  width: 200px;
  margin: 10px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  text-align: center;
}

.movie-card img {
  max-width: 100%;
  height: auto;
}

/* Movie Page styles */
#movie-page-container {
  text-align: center;
}

#movie-page-container img {
  max-width: 300px;
  height: auto;
  margin-bottom: 10px;
}

/* My Favorite Movies Page styles */
#favorites-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.favorite-movie-card {
  width: 200px;
  margin: 10px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  text-align: center;
}

.favorite-movie-card img {
  max-width: 100%;
  height: auto;
}
const apiKey = 'YOUR_OMDB_API_KEY';
const searchInput = document.getElementById('search-input');
const searchResultsContainer = document.getElementById('search-results-container');
const favoritesContainer = document.getElementById('favorites-container');

let favoritesList = [];

// Function to fetch movie data from the API based on the user's search query
async function fetchMovies(searchQuery) {
  const response = await fetch(`https://www.omdbapi.com/?apikey=${apiKey}&s=${searchQuery}`);
  const data = await response.json();
  return data.Search;
}

// Function to render search results
function renderSearchResults(movies) {
  searchResultsContainer.innerHTML = '';
  movies.forEach((movie) => {
    const movieCard = document.createElement('div');
    movieCard.classList.add('movie-card');
    movieCard.innerHTML = `
      <img src="${movie.Poster}" alt="${movie.Title}">
      <h3>${movie.Title}</h3>
      <button class="favorite-btn">Add to Favorites</button>
    `;

    const favoriteBtn = movieCard.querySelector('.favorite-btn');
    favoriteBtn.addEventListener('click', () => {
      addToFavorites(movie);
      navigateToMoviePage(movie.imdbID);
    });

    searchResultsContainer.appendChild(movieCard);
  });
}

// Function to fetch movie details by ID
async function fetchMovieDetails(movieID) {
  const response = await fetch(`https://www.omdbapi.com/?apikey=${apiKey}&i=${movieID}`);
  const data = await response.json();
  return data;
}

// Function to render movie details on the movie page
async function renderMovieDetails() {
  const urlParams = new URLSearchParams(window.location.search);
  const movieID = urlParams.get('movieID');
  const movieDetails = await fetchMovieDetails(movieID);

  const moviePageContainer = document.getElementById('movie-page-container');
  moviePageContainer.innerHTML = `
    <img src="${movieDetails.Poster}" alt="${movieDetails.Title}">
    <h1>${movieDetails.Title}</h1>
    <p>${movieDetails.Plot}</p>
    <!-- Display other movie details as desired -->
  `;
}

// Function to navigate to the movie page
function navigateToMoviePage(movieID) {
  window.location.href = `movie.html?movieID=${movieID}`;
}

// Function to add a movie to the favorites list
function addToFavorites(movie) {
  if (!favoritesList.some((favMovie) => favMovie.imdbID === movie.imdbID)) {
    favoritesList.push(movie);
    renderFavorites();
  }
}

// Function to remove a movie from the favorites list
function removeFromFavorites(movie) {
  favoritesList = favoritesList.filter((favMovie) => favMovie.imdbID !== movie.imdbID);
  renderFavorites();
}

// Function to render favorite movies
function renderFavorites() {
  favoritesContainer.innerHTML = '';
  favoritesList.forEach((movie) => {
    const favoriteMovieCard = document.createElement('div');
    favoriteMovieCard.classList.add('favorite-movie-card');
    favoriteMovieCard.innerHTML = `
      <img src="${movie.Poster}" alt="${movie.Title}">
      <h3>${movie.Title}</h3>
      <button class="remove-btn">Remove from Favorites</button>
    `;

    const removeBtn = favoriteMovieCard.querySelector('.remove-btn');
    removeBtn.addEventListener('click', () => removeFromFavorites(movie));

    favoritesContainer.appendChild(favoriteMovieCard);
  });
}

// Save favoritesList to Local Storage
function saveFavoritesToLocalStorage() {
  localStorage.setItem('favoritesList', JSON.stringify(favoritesList));
}

// Load favoritesList from Local Storage
function loadFavoritesFromLocalStorage() {
  const favorites = localStorage.getItem('favoritesList');
  if (favorites) {
    favoritesList = JSON.parse(favorites);
    renderFavorites();
  }
}

// Call loadFavoritesFromLocalStorage() when the page loads to load the favorites from Local Storage
window.addEventListener('load', loadFavoritesFromLocalStorage);

// Save favoritesList to Local Storage whenever it is updated
window.addEventListener('beforeunload', saveFavoritesToLocalStorage);

// Event listener for the search input
searchInput.addEventListener('input', async (event) => {
  const searchQuery = event.target.value;
  if (searchQuery.length >= 3) {
    const movies = await fetchMovies(searchQuery);
    renderSearchResults(movies);
  }
});
