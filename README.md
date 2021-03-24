# GA Project 1 - Top 5 Movies
I am a big movie buff. So when we were asked to pick an API for Project 1, I naturally gravitated towards the movies API. I built a very simple application that generates a list of 5 random movies from the [OMDB API]('http://omdbapi.com/). The movies are displayed as tiles/cards on the main screen. The user can then click the movie card to bring up an overlay with additional details about the movie. 

## Live Links
Github: 
- https://breadfan18.github.io/project1-movies/

Netlify:
- https://movie-project1-swaroop.netlify.app/

## Technologies Used
- HTML5
- CSS3
    - CSS Flexbox
    - CSS Grid
- Javascript
- jQuery
- OMBD API
- IFRAME
- AJAX

## Wireframes 
These were the wireframes I created before starting to write any code for this project. The actual style and layout ended up a bit different, especially on the overlay for Movie Details

### Main Screen
![mainscreenLayout](https://i.imgur.com/eiBBmJ0.png)

### Movie Details Screen
![movieDetailsLayout](https://i.imgur.com/snihUDH.png)


## Screenshots

### Landing Page
![landingPageLayout](https://i.imgur.com/xGuMYIs.png)

### Main Movie Screen
![movieListLayout](https://i.imgur.com/BOWb46K.jpg)

### Movie Details Overlay
![movieDetailsOverlay](https://i.imgur.com/Go4GPlG.png)

### Movie Details Overlay - RESPONSIVE
![movieDetailsOverlay Responsive](https://i.imgur.com/abz1J4D.png)


## Challenges
### The need to work with multiple URLs

#### Problem
- The `OMBD API` can only return results for a single movie at a time. So i needed to create a `get request` for each movie, and create a URL specific to that movie. 
- This created the issue where i had to include the `.ajax()` function in a loop and call it several times, and get Data from each URL and throw that data into an `Array` of `Objects`. 
- However, i HAD to call the `render()` function within the loop, which meant that the `render()` function also ran through each loop, which is not desired. 
- Running it outside of the `.then()` function meant that my rednder function wouldn't populate with data, since the `promise` wasn't returned. 

```javascript 
function getData() {
    for (const url of createUrlArray(createShortMovieArr())) {
        $.ajax(url)
            .then(function (movieData) {
                    if (movieData.hasOwnProperty('Error')) return;
                    finalDataArray.push(movieData);
                    render();
                },
                function error(movieData) {
                    console.log(movieData);
                })
    }
}
```

#### Solution
- Jumped on Mike's offie hours. 
- He told me about a certain function called `setTimeout()`, where you can set a timeout for its contents to wait before running. 
- Added the `render()` function inside the `setTimeout()` so that it won't run until the timeout (1000ms) runs out, and that's enough time for the `.then()` to return the `promise`
```javascript 
function getData() {
    for (const url of createUrlArray(createShortMovieArr())) {
        $.ajax(url)
            .then(function (movieData) {
                    if (movieData.hasOwnProperty('Error')) return;
                    finalDataArray.push(movieData);
                },
                function error(movieData) {
                    console.log(movieData);
                })
    }
    // Need to run the render() inside setTimeout in order to give enough time for the promise to return all the data and 
    // store it in the finallDataArray before doing anything with it. 
    setTimeout(() => {
        render();
    }, 1000);

}
```

### API returned "Not Found" for some movie titles

#### Problem 
- I was selecting movie titles from a large `movies` object within my `script.js` file, and then appending that movie title to the URL before sending the get request
- But some movie titles were returning `Error: "Not found"` from the OMBD API.
- So i needed a way to filter out the ones returning Error before rendering movies on the DOM

#### Solution
- Increaased the total array size of movies to 20 instead of 5
- Then inside the `getData()` function, added a condition to `return` if the movie data being returned has a `key` of "Error". 
- Then i took the final array size, and sliced it to 5. 
- The final array that was a size of 5 always had 5 movies without any errors. 

## Future Enhancements
- Add placeholder cards on the landing page so it doesn't look so empty.
- Give the user the option to select a Genre, and only return results for that Genre from the API
- Embed movie trailers to the Movie Details overlay. May have to hit another API to return trailers for a given movie, since OMDB API doesn't return trailers. 