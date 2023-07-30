const fetchMovieData = async (movieTitle) => {
  const response = await fetch(`https://www.omdbapi.com/?t=${movieTitle}`);
  const data = await response.json();
  return data;
};

const HomePage = () => {
  const [movieTitle, setMovieTitle] = useState("");
  const [movieData, setMovieData] = useState(null);

  const handleChange = (event) => {
    setMovieTitle(event.target.value);
  };

  const handleSubmit = async (event) => {
    event.preventDefault();
    const data = await fetchMovieData(movieTitle);
    setMovieData(data);
  };

  return (
    <div>
      <h1>Home Page</h1>
      <input
        type="text"
        placeholder="Enter movie title"
        value={movieTitle}
        onChange={handleChange}
      />
      <button onClick={handleSubmit}>Search</button>
      {movieData && (
        <div>
          <h2>{movieData.Title}</h2>
          <img src={movieData.Poster} alt={movieData.Title} />
          <p>{movieData.Plot}</p>
        </div>
      )}
    </div>
  );
};

const MoviePage = () => {
  const [movieData, setMovieData] = useState(null);

  const handleFavorite = () => {
    const favorites = JSON.parse(localStorage.getItem("favorites"));
    if (favorites === null) {
      favorites = [];
    }
    if (favorites.indexOf(movieData.Title) === -1) {
      favorites.push(movieData.Title);
      localStorage.setItem("favorites", JSON.stringify(favorites));
    }
  };

  return (
    <div>
      <h1>Movie Page</h1>
      <h2>{movieData.Title}</h2>
      <img src={movieData.Poster} alt={movieData.Title} />
      <p>{movieData.Plot}</p>
      <button onClick={handleFavorite}>Add to Favorites</button>
    </div>
  );
};

const MyFavoriteMoviesPage = () => {
  const favorites = JSON.parse(localStorage.getItem("favorites"));
  if (favorites === null) {
    favorites = [];
  }

  return (
    <div>
      <h1>My Favorite Movies</h1>
      {favorites.map((movieTitle) => (
        <div key={movieTitle}>
          <h2>{movieTitle}</h2>
          <button onClick={() => {
            const newFavorites = favorites.filter((movie) => movie !== movieTitle);
            localStorage.setItem("favorites", JSON.stringify(newFavorites));
          }}>
            Remove from Favorites
          </button>
        </div>
      ))}
    </div>
  );
};

body {
  font-family: sans-serif;
  margin: 0;
  padding: 0;
}

h1 {
  font-size: 24px;
  margin: 0 0 15px 0;
}

h2 {
  font-size: 18px;
  margin: 0 0 10px 0;
}

p {
  font-size: 16px;
  margin: 0 0 15px 0;
}

a {
  color: #000;
  text-decoration: none;
}

.container {
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
}

.movie-card {
  margin: 0 0 20px 0;
  border: 1px solid #ccc;
  padding: 20px;
}

.movie-card img {
  width: 100%;
}

.movie-card h2 {
  margin: 0 0 10px 0;
}

.movie-card p {
  margin: 0 0 15px 0;
}

.movie-card button {
  background-color: #000;
  color: #fff;
  padding: 10px 20px;
  border: none;
  cursor: pointer;
}

import React, { useState } from "react";

const SearchBar = ({ value }) => {
  const [searchTerm, setSearchTerm] = useState("");

  const handleChange = (event) => {
    setSearchTerm(event.target.value);
  };

  const handleSubmit = () => {
    // TODO: Search for movies by title
  };

  return (
    <div>
      <input
        type="text"
        placeholder="Search movies"
        value={searchTerm}
        onChange={handleChange}
      />
      <button onClick={handleSubmit}>Search</button>
    </div>
  );
};

export default SearchBar;

import React from "react";
import SearchBar from "./search";

const HomePage = () => {
  return (
    <div>
      <h1>Home Page</h1>
      <SearchBar />
    </div>
  );
};

export default HomePage;

import React, { useState } from "react";

const Rating = ({ rating }) => {
  const [selectedRating, setSelectedRating] = useState(1);

  const handleChange = (event) => {
    setSelectedRating(event.target.value);
  };

  return (
    <div>
      <h2>Rate this movie</h2>
      <input
        type="radio"
        value="1"
        name="rating"
        onChange={handleChange}
        checked={selectedRating === 1}
      />
      <label htmlFor="rating-1">1 star</label>
      <input
        type="radio"
        value="2"
        name="rating"
        onChange={handleChange}
        checked={selectedRating === 2}
      />
      <label htmlFor="rating-2">2 stars</label>
      <input
        type="radio"
        value="3"
        name="rating"
        onChange={handleChange}
        checked={selectedRating === 3}
      />
      <label htmlFor="rating-3">3 stars</label>
      <input
        type="radio"
        value="4"
        name="rating"
        onChange={handleChange}
        checked={selectedRating === 4}
      />
      <label htmlFor="rating-4">4 stars</label>
      <input
        type="radio"
        value="5"
        name="rating"
        onChange={handleChange}
        checked={selectedRating === 5}
      />
      <label htmlFor="rating-5">5 stars</label>
    </div>
  );
};

export default Rating;

import React from "react";
import Rating from "./rating";

const MoviePage = () => {
  const [rating, setRating] = useState(1);

  return (
    <div>
      <h1>Movie Page</h1>
      <Rating rating={rating} />
    </div>
  );
};

export default MoviePage;

import React, { useState } from "react";

const Watchlist = ({ movies }) => {
  const [watchlist, setWatchlist] = useState([]);

  const handleAddMovie = (movie) => {
    setWatchlist((prevWatchlist) => {
      return [...prevWatchlist, movie];
    });
  };

  const handleRemoveMovie = (movie) => {
    setWatchlist((prevWatchlist) => {
      return prevWatchlist.filter((m) => m !== movie);
    });
  };

  return (
    <div>
      <h2>My Watchlist</h2>
      <ul>
        {movies.map((movie) => (
          <li key={movie}>
            {movie}
            <button onClick={() => handleRemoveMovie(movie)}>
              Remove
            </button>
          </li>
        ))}
      </ul>
      <button onClick={() => handleAddMovie("New Movie")}>
        Add Movie
      </button>
    </div>
  );
};

export default Watchlist;

import React from "react";
import Watchlist from "./watchlist";

const MoviePage = () => {
  const [movies, setMovies] = useState([]);

  return (
    <div>
      <h1>Movie Page</h1>
      <Watchlist movies={movies} />
    </div>
  );
};

export default MoviePage;

