# movie-details
COMPANY: CODITECH SOLUTIONS
NAME: MOHAMED MUFLIH
INTERN ID:CT08NLU
DOMAIN:REACT
BATCH DURATION:january 15th/2025 to february 15th/2025
This React app provides an interface for users to search for movies, view movie details, and save their favourite movies using the OMDb API. The application allows users to perform searches by movie title, displays relevant movie information, and offers the functionality to save movies to local storage as favourites. Here’s a detailed breakdown of how the app works:

### 1. **Setting Up State Variables:**

The app uses the `useState` hook to manage three state variables:
- **`searchQuery`**: This holds the current search query entered by the user. It’s updated whenever the user types something in the search input field.
- **`movies`**: This state stores the list of movies returned from the OMDb API. It’s populated when the user performs a search.
- **`favorites`**: This state stores an array of the user’s favourite movies, which are persisted in the browser’s local storage. It’s initially populated using `useEffect` (explained below).

```javascript
const [searchQuery, setSearchQuery] = useState('');
const [movies, setMovies] = useState([]);
const [favorites, setFavorites] = useState([]);
```

### 2. **Fetching Movie Data:**

The function `handleSearch` is responsible for querying the OMDb API and fetching movie details based on the search query. The API request is made using Axios, a promise-based HTTP client for making requests.

```javascript
const handleSearch = async () => {
  if (!searchQuery) return;  // If no query is entered, do nothing

  try {
    const response = await axios.get(API_URL, {
      params: {
        s: searchQuery,  // Search term from the input field
        apiKey: API_KEY,  // OMDb API key
      },
    });
    
    // Handle valid search results
    if (response.data.Response === 'True') {
      setMovies(response.data.Search);
    } else {
      setMovies([]);  // If no results, clear the movie list
    }
  } catch (error) {
    console.error('Error fetching movie data:', error);  // Log errors
  }
};
```

When the user types a query and clicks the search button, `handleSearch` fetches movie data. If the response contains valid results, the `movies` state is updated. If the response does not contain results (like if no movies match the search term), the `movies` state is cleared.

### 3. **Adding and Removing Movies from Favourites:**

The app allows users to add movies to their favourites by clicking the **Add to Favorites** button for each movie. The `handleAddToFavorites` function is triggered when a user clicks this button. It checks if the movie is already in the favourites list (to prevent duplicates) and updates the `favorites` state. This state is then saved to `localStorage` so that the favourites persist even after the page is refreshed.

```javascript
const handleAddToFavorites = (movie) => {
  // Prevent duplicates
  if (!favorites.some(fav => fav.imdbID === movie.imdbID)) {
    const newFavorites = [...favorites, movie];
    setFavorites(newFavorites);
    localStorage.setItem('favorites', JSON.stringify(newFavorites));  // Save to localStorage
  }
};
```

The `handleRemoveFromFavorites` function allows users to remove a movie from their favourites. When triggered, it filters out the movie from the `favorites` array and updates `localStorage` accordingly.

```javascript
const handleRemoveFromFavorites = (movieID) => {
  const updatedFavorites = favorites.filter(fav => fav.imdbID !== movieID);
  setFavorites(updatedFavorites);
  localStorage.setItem('favorites', JSON.stringify(updatedFavorites));  // Update localStorage
};
```

### 4. **Loading Favourites from Local Storage:**

When the app is first loaded, the `useEffect` hook is used to retrieve the saved favourites from `localStorage` and populate the `favorites` state. This ensures that the list of favourite movies persists between page reloads.

```javascript
useEffect(() => {
  const savedFavorites = JSON.parse(localStorage.getItem('favorites')) || [];
  setFavorites(savedFavorites);  // Load saved favourites from localStorage
}, []);
```

### 5. **Rendering the User Interface:**

The app displays two main sections:
1. **Search Results**: Shows a list of movies returned from the search query. Each movie includes a title, poster, year, and a button to add it to favourites.
2. **Favourite Movies**: Displays a list of movies saved as favourites. Each movie has a button to remove it from favourites.

```javascript
<div className="movies-list">
  {movies.length === 0 ? (
    <p>No movies found.</p>
  ) : (
    movies.map(movie => (
      <div key={movie.imdbID} className="movie-card">
        <img src={movie.Poster !== 'N/A' ? movie.Poster : 'https://via.placeholder.com/150'} alt={movie.Title} />
        <h3>{movie.Title}</h3>
        <p>{movie.Year}</p>
        <button onClick={() => handleAddToFavorites(movie)}>
          Add to Favorites
        </button>
      </div>
    ))
  )}
</div>
```

Each movie card includes a poster, title, year, and the option to add or remove it from the favourites list. The app makes use of conditional rendering to display appropriate messages like "No movies found" or "No favourites added yet."

### 6. **Conclusion:**

This React app combines search functionality, external API integration (OMDb), and local storage to create an interactive movie browsing experience. Users can search for movies, view movie details, and save their favourite movies to be accessed later. By using state management for the movie list and favourites, as well as the `useEffect` hook for loading and saving data, the app offers an easy and efficient way to explore movies and store personal preferences. This app can be extended further with features like displaying detailed movie information, pagination for search results, and improved error handling.
