# Scripts and Stuff
## Included resources
Move the `<script src="..."></script>` content at the bottom of the code into the beginning inside the `<head>` tag  
```
  <head>
    ...
    <script src="..."></script>
    <script src="..."></script>
  </head>
```
## Writing your own javascript
Before the end of the `</head>` tag, create another script tag like  
```
        ...
      <script>
        // We will put things here later
      </script>
    </head>
```

## Loading Bookings
The website will need to display bookings when it loads.    
First we need to identify the table so we can populate it.  
Find the table tag `<table class="table table-striped">` and add a class `bookings` 
```
    <table class="bookings table table-striped">
```
We want to keep the table headings, but we can delete all the existing data and also add an extra `<th>` for some buttons later on.
So the table looks like
```
    <table class="bookings table table-striped">
      <thead>
        <tr>
          <th>DOCKET#</th>
          <th>Date</th>
          <th>First Name</th>
          <th>Last Name</th>
          <th>Period</th>
          <th>Package</th>
          <th>Status</th>
          <th>Start</th>
          <th>End</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
      </tbody>
    </table>
```
For the javascript, we will put all our code just underneath the new `<script>` tag we created. The odd syntax `$(function(){` just means execute the code when the page has finished loading. 
```
    <script>
        var bookings;// Global variable to store all our bookings
        
        function loadBookings() {
            // our code will go here
        }
        
        function appendBooking(booking) {
            // our code will go here
        }
        
        $(function() {
            // executes when page has finished downloading
            loadBookings()
        })
    </script>
```
Firstly we would want to load all the bookings from local storage. If there are no bookings, lets make an empty list.
```
    function loadBookings() {
        bookings = JSON.parse(localStorage.getItem("bookings"));
        if (bookings) {
            // code will go here
        } else {
            bookings = [];
        }
    }
```
Then we want to do something for each item in the array
``` 
    function loadBookings() {
        bookings = JSON.parse(localStorage.getItem("bookings"));
        if (bookings) {
            bookings.forEach(function(item, index) {
                appendBooking(item);
            });
        } else {
            bookings = [];
        }
    }
```
For each item, we need to make a new row `<tr>` in the table, and for each item in the booking we want to create a `<td>` for it and set the text in the `<td>` to be the value of the booking object. Then finally, we would append the `<tr>` we made to the table
```
      function appendBooking(item) {
          let row = $("<tr>");
          Object.entries(item).forEach(([key, value]) => {
              row.append( $("<td>").text(value).attr({"docket": item.docket, "type": key, "contenteditable": true}) )
          });
          row.append($("<td>").text("Update").attr("docket", item.docket).on("click", function() {
            updateBooking(item.docket)
          }));
          $("table.bookings tbody").append(row);
      }
```

## Creating a new booking
To create a new booking, lets add a row at the top of the table that lets you enter data and add a booking
```
    <table class="bookings table table-striped">
      <thead>
        <tr>
          <th>DOCKET#</th>
          <th>Date</th>
          <th>First Name</th>
          <th>Last Name</th>
          <th>Period</th>
          <th>Package</th>
          <th>Status</th>
          <th>Start</th>
          <th>End</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><input id="input_docket" type="text" style="width: 5em;" class="input_newBooking" placeholder="Docket #" /></td>
          <td><input id="input_date" type="text" style="width: 6em;" class="input_newBooking" placeholder="DD/MM/YY" /></td>
          <td><input id="input_firstName" type="text" style="width: 6em;" class="input_newBooking" placeholder="First Name" /></td>
          <td><input id="input_lastName" type="text" style="width: 6em;" class="input_newBooking" placeholder="Last Name" /></td>
          <td><input id="input_period" type="text" style="width: 3em;" class="input_newBooking" placeholder="Days" /></td>
          <td><input id="input_package" type="text" style="width: 6em;" class="input_newBooking" placeholder="Package" /></td>
          <td><input id="input_status" type="text" style="width: 6em;" class="input_newBooking" placeholder="Status" /></td>
          <td><input id="input_start" type="text" style="width: 6em;" class="input_newBooking" placeholder="DD/MM/YY" /></td>
          <td><input id="input_end" type="text" style="width: 6em;" class="input_newBooking" placeholder="DD/MM/YY" /></td>
          <td><span id="button_addNewBooking">Add</span></td>
        </tr>
      </tbody>
    </table>
```
For the scripts, below the function `function displayBooking() { ... }` we created earlier, lets add a new function for creating bookings
```
    function createBooking() {
        let booking = {
            docket: $("#input_docket").val(),
            date: $("#input_date").val(),
            firstName: $("#input_firstName").val(),
            lastName: $("#input_lastName").val(),
            period: $("#input_period").val(),
            package: $("#input_package").val(),
            status: $("#input_status").val(),
            start: $("#input_start").val(),
            end: $("#input_end").val(),
        };
        bookings.push(booking);
        appendBooking(booking);
        localStorage.setItem("bookings", JSON.stringify(bookings)); // Save the bookings in local storage
    }
```
To call this function, we will add an listener in `$(function() { ... }` to call the function when the add button is clicked
```
    $(function() {
        // executes when page has finished downloading
        loadBookings();
        $("#button_addNewBooking").on("click", function() {
            createBooking();
        });
    });
```

## Updating an existing booking
In the `appendBooking` function 
```
    function appendBooking(item) {
        let row = $("<tr>");
        Object.entries(item).forEach(([key, value]) => {
            row.append( $("<td>").text(value) )
        });
        $("table.bookings tbody").append(row);
    }
```
We want to be able to identify which row it is so lets add a docket number attribute to each row as well as what type of value it is
```
    function appendBooking(item) {
        let row = $("<tr>");
        Object.entries(item).forEach(([key, value]) => {
            row.append( $("<td>").text(value).attr({"docket": item.docket, "type": key}) )
        });
        $("table.bookings tbody").append(row);
    }
```
Then we need to set contenteditable = true to allow you to edit the content by just simply clicking on it
```
    function appendBooking(item) {
        let row = $("<tr>");
        Object.entries(item).forEach(([key, value]) => {
            row.append( $("<td>").text(value).attr({"docket": item.docket, "type": key, "contenteditable": true}) )
        });
        $("table.bookings tbody").append(row);
    }
```
And finally lets add an update button at the end that we can use to call an `updateBooking` function we will make later
```
    function appendBooking(item) {
        let row = $("<tr>");
        Object.entries(item).forEach(([key, value]) => {
            row.append( $("<td>").text(value).attr({"docket": item.docket, "type": key, "contenteditable": true}) )
        });
        row.append($("<td>").text("Update").attr("docket", item.docket).on("click", function() {
            updateBooking(item.docket)
        }));
        $("table.bookings tbody").append(row);
    }
```
we only need to make the updateBooking function. Add it just like the other functions we added.
```
    function updateBooking(docket) {
        // Code will go here
    }
```
We want to make a booking object first with docket id being the supplied docket id
```
    function updateBooking(docket) {
        let booking = {"docket": docket};
    }
```
We want to make a list of attributes we want to update and loop through it and update all the values in booking
```
    function updateBooking(docket) {
        let booking = {"docket": docket};
        let attributes = ["date", "firstName", "lastName", "period", "package", "status", "start", "end"];
        attributes.forEach(function(attr) {
            booking[attr] = $("td[docket="+docket+"][type="+attr+"]").text()
        });
    }
```
Next we want to update the new booking in our bookings array, set the item in local storage and alert the user you successfully updated a booking
```
    function updateBooking(docket) {
        let booking = {"docket": docket};
        let attributes = ["date", "firstName", "lastName", "period", "package", "status", "start", "end"];
        attributes.forEach(function(attr) {
            booking[attr] = $("td[docket="+docket+"][type="+attr+"]").text()
        });
        let index = bookings.findIndex(x => x.docket == docket);
        bookings[index] = booking;
        localStorage.setItem("bookings", JSON.stringify(bookings));
        alert("Successfully Updated!");
    }
```
## Remove a booking
To remove a booking, lets modify the `appendBooking` function a bit to also add an `Remove` button in addition to `Update`.  
Instead of just appending a td, we should append a td with three `<span>` elements inside like so
```
    row.append($("<td>").append([
        $("<span>").text("Update").attr("docket", item.docket).on("click", function() {
            updateBooking(item.docket)
        }),
        $("<span>").text(" "),
        $("<span>").text("Remove").attr("docket", item.docket).on("click", function() {
            removeBooking(item.docket)
        })
    ]));
```
Now lets write our `removeBooking` function. We get the index just like the `updateBooking` function above
```
    function removeBooking(docket) {
        let index = bookings.findIndex(x => x.docket == docket);
    }
```
Splice removes an element at index from the array
```
    function removeBooking(docket) {
        let index = bookings.findIndex(x => x.docket == docket);
        bookings.splice(index, 1);
    }
```
Then we save it to local storage
```
    function removeBooking(docket) {
        let index = bookings.findIndex(x => x.docket == docket);
        bookings.splice(index, 1);
        localStorage.setItem("bookings", JSON.stringify(bookings));
    }
```
And remove it from the screen
```
    function removeBooking(docket) {
        let index = bookings.findIndex(x => x.docket == docket);
        bookings.splice(index, 1);
        localStorage.setItem("bookings", JSON.stringify(bookings));
        $("td[docket="+docket+"][type=docket]").parent().remove();
    }
```

# Styling
We would also want to style our elements, so in the `<head>` tag after all our scripts, add a `<style>` tag
```
    <head>
        ...
        <script>
            ...
        </script>
        <style>
            // our code will go here
        </style>
    </head>
```
## Inputs
In the style tag we will add a styling for the class `input_newBooking` which is the class we gave to all the input elements we made
```
    <style>
        .input_newBooking {
            text-align: center;
        }
    </style>
```
##  Buttons
Lets also add styling for the buttons we added
```
    <style>
        .input_newBooking {
            text-align: center;
        }
        .btn_add:hover, .btn_update:hover, .btn_remove:hover {
            cursor: pointer;
            text-decoration: underline;
        }
        .btn_add:hover {
            color: darkgreen;
        }
        .btn_update:hover {
            color: purple;
        }
        .btn_remove:hover {
            color: crimson;
        }
    </style>
```
And add the class to the add button in the `<table>`
```
    <td><span id="button_addNewBooking" class="btn_add">Add</span></td>
```
And the update / remove button in `appendBooking`
```
    function appendBooking(item) {
          let row = $("<tr>");
          Object.entries(item).forEach(([key, value]) => {
              row.append( $("<td>").text(value).attr({"docket": item.docket, "type": key, "contenteditable": true}) )
          });
          row.append($("<td>").append([
        $("<span>").text("Update").addClass("btn_update").attr("docket", item.docket).on("click", function() {
            updateBooking(item.docket)
        }),
        $("<span>").text(" "),
        $("<span>").text("Remove").addClass("btn_remove").attr("docket", item.docket).on("click", function() {
            removeBooking(item.docket)
        })
    ]));
```
