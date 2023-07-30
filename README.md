For this project, we will use HTML, CSS, and JavaScript for the frontend, and Node.js with Express for the backend.

Home Page (index.html)
html
<!DOCTYPE html>
<html>
<head>
    <title>IMDB Clone</title>
</head>
<body>
    <h1>IMDB Clone</h1>
    <input type="text" id="searchInput" placeholder="Search movies...">
    <ul id="searchResults"></ul>
</body>
</html>
Frontend JavaScript (index.js)
javascript
const searchInput = document.getElementById("searchInput");
const searchResults = document.getElementById("searchResults");

searchInput.addEventListener("input", () => {
    const searchTerm = searchInput.value;
    // Call the API with searchTerm and get search results
    // Display the search results in the searchResults list
});

searchResults.addEventListener("click", (event) => {
    if (event.target.className === "favorite-button") {
        const movieId = event.target.dataset.movieId;
        // Add the movie to the favorite list (using API or local storage)
    }
});
Movie Page (movie.html)
html
<!DOCTYPE html>
<html>
<head>
    <title>Movie Details</title>
</head>
<body>
    <h1 id="movieTitle"></h1>
    <img id="movieImage" src="" alt="Movie Poster">
    <p id="moviePlot"></p>
</body>
</html>
Frontend JavaScript (movie.js)
javascript
Copy code
const movieTitle = document.getElementById("movieTitle");
const movieImage = document.getElementById("movieImage");
const moviePlot = document.getElementById("moviePlot");

// Get the movieId from the URL (you can use URLSearchParams or other techniques)
const movieId = getMovieIdFromURL();

// Call the API with movieId and get the movie details
// Display the movie details in the respective elements
My Favourite Movies Page (favorites.html)
html
Copy code
<!DOCTYPE html>
<html>
<head>
    <title>My Favourite Movies</title>
</head>
<body>
    <h1>My Favourite Movies</h1>
    <ul id="favoriteMovies"></ul>
</body>
</html>
Frontend JavaScript (favorites.js)
javascript
const favoriteMoviesList = document.getElementById("favoriteMovies");

// Fetch the favorite movies from local storage or API
// Display the list of favorite movies in the favoriteMoviesList

favoriteMoviesList.addEventListener("click", (event) => {
    if (event.target.className === "remove-button") {
        const movieId = event.target.dataset.movieId;
        // Remove the movie from the favorite list (using API or local storage)
    }
});
Here's a simplified example of how the backend might look:
// server.js
const express = require('express');
const app = express();
const port = 3000;

// Set up routes to handle API requests
app.get('/api/search/:query', (req, res) => {
    const searchTerm = req.params.query;
    // Call the external API (e.g., OMDB API) to get search results
    // Return the search results as JSON
});

app.get('/api/movies/:movieId', (req, res) => {
    const movieId = req.params.movieId;
    // Call the external API (e.g., OMDB API) to get movie details
    // Return the movie details as JSON
});

// Set up more routes for adding/removing favorite movies and retrieving the favorites list

app.listen(port, () => console.log(`Server is running on port ${port}`));
// Assuming you have a favoritesController.js file to handle favorites operations

// Add a movie to favorites
app.post('/api/favorites', favoritesController.addToFavorites);

// Remove a movie from favorites
app.delete('/api/favorites/:movieId', favoritesController.removeFromFavorites);
Backend - favoritesController.js
// Assuming you have implemented a data store or database for storing favorite movies

const favorites = []; // This is just a simple in-memory array; in a real app, use a database

function addToFavorites(req, res) {
    const movieId = req.body.movieId;
    // Check if the movie is already in the favorites list
    if (!favorites.some((movie) => movie.id === movieId)) {
        // Fetch the movie details from the API using movieId and add it to favorites
        const movieDetails = fetchMovieDetailsFromAPI(movieId);
        favorites.push(movieDetails);
    }
    res.status(200).json({ message: 'Movie added to favorites successfully' });
}

function removeFromFavorites(req, res) {
    const movieId = req.params.movieId;
    const index = favorites.findIndex((movie) => movie.id === movieId);
    if (index !== -1) {
        favorites.splice(index, 1);
    }
    res.status(200).json({ message: 'Movie removed from favorites successfully' });
}

module.exports = { addToFavorites, removeFromFavorites };
Frontend JavaScript - Adding to Favorites
// Inside the searchResults event listener
searchResults.addEventListener("click", (event) => {
    if (event.target.className === "favorite-button") {
        const movieId = event.target.dataset.movieId;
        // Call the API to add the movie to favorites
        fetch('/api/favorites', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({ movieId })
        })
        .then((response) => response.json())
        .then((data) => {
            console.log(data.message); // Show success message to the user
        })
        .catch((error) => {
            console.error('Error adding movie to favorites:', error);
        });
    }
});
Frontend JavaScript - Removing from Favorites
// Inside the favoriteMoviesList event listener
favoriteMoviesList.addEventListener("click", (event) => {
    if (event.target.className === "remove-button") {
        const movieId = event.target.dataset.movieId;
        // Call the API to remove the movie from favorites
        fetch(`/api/favorites/${movieId}`, {
            method: 'DELETE',
        })
        .then((response) => response.json())
        .then((data) => {
            console.log(data.message); // Show success message to the user
            // Optionally, remove the movie from the frontend list as well
        })
        .catch((error) => {
            console.error('Error removing movie from favorites:', error);
        });
    }
});
Fetching and Displaying Search Results
// Frontend JavaScript (index.js)
const searchInput = document.getElementById("searchInput");
const searchResults = document.getElementById("searchResults");

searchInput.addEventListener("input", () => {
    const searchTerm = searchInput.value;
    fetch(`/api/search/${searchTerm}`)
        .then((response) => response.json())
        .then((data) => {
            searchResults.innerHTML = ""; // Clear previous search results
            data.forEach((movie) => {
                const li = document.createElement("li");
                li.innerHTML = `
                    <div>
                        <img src="${movie.poster}" alt="${movie.title} Poster">
                    </div>
                    <div>
                        <h3>${movie.title}</h3>
                        <p>${movie.year}</p>
                        <button class="favorite-button" data-movie-id="${movie.id}">Add to Favorites</button>
                    </div>
                `;
                searchResults.appendChild(li);
            });
        })
        .catch((error) => {
            console.error('Error fetching search results:', error);
        });
});
Fetching and Displaying Movie Details
// Frontend JavaScript (movie.js)
const movieTitle = document.getElementById("movieTitle");
const movieImage = document.getElementById("movieImage");
const moviePlot = document.getElementById("moviePlot");

// Get the movieId from the URL
const movieId = getMovieIdFromURL();

fetch(`/api/movies/${movieId}`)
    .then((response) => response.json())
    .then((data) => {
        movieTitle.textContent = data.title;
        movieImage.src = data.poster;
        movieImage.alt = data.title + " Poster";
        moviePlot.textContent = data.plot;
    })
    .catch((error) => {
        console.error('Error fetching movie details:', error);
    });
Backend - Fetch Movie Details
// Assuming you have implemented a function to fetch movie details from an external API
// For example, using the "axios" library

const axios = require('axios');

async function fetchMovieDetails(movieId) {
    try {
        const response = await axios.get(`https://api.example.com/movies/${movieId}`);
        return response.data;
    } catch (error) {
        console.error('Error fetching movie details:', error);
        throw new Error('Failed to fetch movie details');
    }
}

// In your moviesController.js file
async function getMovieDetails(req, res) {
    const movieId = req.params.movieId;
    try {
        const movieDetails = await fetchMovieDetails(movieId);
        res.json(movieDetails);
    } catch (error) {
        res.status(500).json({ error: 'Failed to fetch movie details' });
    }
}

module.exports = { getMovieDetails };
Below is a sample CSS code to style the IMDB clone project
/* styles.css */

body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
  background-color: #f2f2f2;
}

h1 {
  text-align: center;
  margin-top: 20px;
}

/* Home Page */

#searchInput {
  width: 100%;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
}

#searchResults {
  list-style: none;
  padding: 0;
}

#searchResults li {
  display: flex;
  align-items: center;
  margin: 10px;
  padding: 10px;
  background-color: #fff;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
}

#searchResults li img {
  width: 80px;
  height: 120px;
  margin-right: 20px;
}

.favorite-button {
  padding: 8px 16px;
  background-color: #007bff;
  color: #fff;
  border: none;
  cursor: pointer;
}

.favorite-button:hover {
  background-color: #0056b3;
}

/* Movie Page */

#movieImage {
  width: 200px;
  height: 300px;
  margin: 20px auto;
  display: block;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

#movieTitle {
  text-align: center;
}

#moviePlot {
  max-width: 800px;
  margin: 20px auto;
}

/* My Favourite Movies Page */

#favoriteMovies {
  list-style: none;
  padding: 0;
}

#favoriteMovies li {
  display: flex;
  align-items: center;
  margin: 10px;
  padding: 10px;
  background-color: #fff;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
}

#favoriteMovies li img {
  width: 60px;
  height: 90px;
  margin-right: 20px;
}

.remove-button {
  padding: 8px 16px;
  background-color: #dc3545;
  color: #fff;
  border: none;
  cursor: pointer;
}

.remove-button:hover {
  background-color: #c82333;
}

/* Responsive styles */

@media (max-width: 768px) {
  #searchResults li {
    flex-direction: column;
  }

  #searchResults li img,
  #favoriteMovies li img {
    width: 100px;
    height: 150px;
    margin: 10px 0;
  }
}
