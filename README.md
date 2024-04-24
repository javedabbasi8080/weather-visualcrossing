# fetchAndDisplayWeatherData Function
    • Fetches weather data from the API endpoint URL.
    • Parses the JSON response.
    • Calls the displayWeatherData function to render the weather data on the webpage.
    • Handles errors if the API request fails.
```javascript
async function fetchAndDisplayWeatherData() {
  try {
    const response = await fetch(apiUrl);
    
    if (!response.ok) {
      throw new Error('Failed to fetch weather data');
    }

    const data = await response.json();
    displayWeatherData(data);

  } catch (error) {
    console.error('Error fetching weather data:', error);
  }
}
```

#displayWeatherData Function
    • Iterates through the weather data object.
    • For each key:
        ◦ If the value is an array, creates a function to display the list data in a table.
        ◦ If the value is an object, recursively calls the displayNestedObject function.
        ◦ If the value is a primitive type, renders it as a key-value pair on the webpage.

function displayWeatherData(data) {
  const weatherDataDiv = document.getElementById('weatherData');
  
  for (const key in data) {
    if (Array.isArray(data[key])) {
      createListDataFunction(key, data[key]);
    }

    if (typeof data[key] === 'object') {
      displayNestedObject(data[key], weatherDataDiv, key);
    } else {
      weatherDataDiv.insertAdjacentHTML('beforeend', `<p><strong>${key}:</strong> ${data[key]}</p>`);
    }
  }
}

#createListDataFunction Function
    • Creates a dynamic function to display the list data in a table when invoked.
    • The function name is based on the key name, prefixed with "display" and capitalized.
    • When invoked, the function clears the weatherDataDiv, adds a heading with the key name, and renders the list data in a table.

function createListDataFunction(key, list) {
  window[`display${capitalizeFirstLetter(key)}Data`] = function() {
    const weatherDataDiv = document.getElementById('weatherData');
    weatherDataDiv.innerHTML = `<h2>${key}</h2>`;
    displayListDataInTable(list, weatherDataDiv);
  };
}

#displayListDataInTable Function
    • Generates a table with column headings based on the keys of the list items.
    • Appends the table to the specified container element.

function displayListDataInTable(list, container) {
  const table = document.createElement('table');
  const keys = Object.keys(list[0]);

  // Generate table header row
  let headerRow = table.insertRow();
  keys.forEach(key => {
    let th = document.createElement('th');
    th.textContent = key;
    headerRow.appendChild(th);
  });

  // Generate table body rows
  list.forEach(item => {
    let row = table.insertRow();
    keys.forEach(key => {
      let cell = row.insertCell();
      cell.textContent = item[key];
    });
  });

  container.appendChild(table);
}


#displayNestedObject Function
    • Recursively iterates through the nested object.
    • For each key:
        ◦ If the value is an array, calls the createListDataFunction to create a function for displaying list data.
        ◦ If the value is an object, recursively calls itself.
        ◦ If the value is a primitive type, renders it as a key-value pair on the webpage with the parent key prefixed.

function displayNestedObject(obj, container, parentKey) {
  for (const key in obj) {
    if (Array.isArray(obj[key])) {
      container.insertAdjacentHTML('beforeend', `<h2>${parentKey}.${key}</h2>`);
      displayListDataInTable(obj[key], container);
    } else if (typeof obj[key] === 'object') {
      displayNestedObject(obj[key], container, `${parentKey}.${key}`);
    } else {
      container.insertAdjacentHTML('beforeend', `<p><strong>${parentKey}.${key}:</strong> ${obj[key]}</p>`);
    }
  }
}

#capitalizeFirstLetter Function
    • Capitalizes the first letter of a string.
    • Returns the input string with the first letter capitalized.

function capitalizeFirstLetter(string) {
  return string.charAt(0).toUpperCase() + string.slice(1);
}
