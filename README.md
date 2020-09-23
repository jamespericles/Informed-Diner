# Informed-Diner-App

The concept of this app is two-fold.  It addresses a demographic-independent human need to eat, with a societal norm to enjoy eating prepared food at a restaurant.  It also addresses concern of the current time (July 2020) as the United States current leads the world in the highest volume of deaths per capita as a result of the Covid-19 virus.  Coming on the heels of some lifting of quarantine restrictions in North Carolina, and still seeing surges in infected case counts, and statewise mandate to observe social distancing and wearing of masks, the application was incepted based on providing users with a means to find open restaurants nearby (as many restaurants at this time observe limited hours or are closed altogether) and provides them with a way to determine how safe a restaurant is based on crowdedness and traffic to it so that they can more planfully decide when to patronize the restaurant.

Our team consisted of myself, [James Pericles](https://github.com/jamespericles), [Colin Shelton](https://github.com/colinshelton) and [Sean McGinnis](https://github.com/srmchartroom), with research assistance and presentation support from [Chris Schram](https://github.com/Schramm9). 

## Data & Logic Flow

The below image shows an early mapping of planned data and logic flow for the application.  Though the profile creation and login has been reserved for MVP2 (you can simply click through it at this time), the flows between the listings and the results page are representative of the current application.

Upon reaching the listing page, the application makes an AJAX call to the Google places API - actually it does this twice.  Initially an AJAX call is made to Google Places' Find Places From Text API which returns the central latitude and longitude coordinates for a given zipcode.  In this initial MVP we've fed a zipcode in of Greensboro (27408), but in MVP2 we will allow the location to be set by the user profile and location search.  The returned coordinates from the Find Places From Text API are then provided to another AJAX API call to Google's NEARBY Places set to return restaurants near the latitude and longitude coordinates.  This returns a JSON object of ~20 restaurants with information such as name, location, average rating, whether it is open now, etc.  This object establishes the content displayed in the restaurant listings.

![Image of Firebase Calls Logic](https://user-images.githubusercontent.com/31550856/87196416-ff8e0780-c2c7-11ea-8142-1ef26abb9e24.png)

Selection of any restaurant in the listings will perform additional logic, first checking a session temporary object returned from a FireBase table holding previous searches.  If certain fields are populated the results details page will populate from the previously aggregated and stored results.  If not, the code will make an AJAX call to Google Places again, but this time to Google Places' Place Detail to return detail about hours of operation, address and contact information, etc.  And an additional AJAX call is made to BestTime.io's API to return forecasted values on business and traffic to the restaurant.

![Image of Data and Logic Flow](https://user-images.githubusercontent.com/31550856/87193319-762a0580-c2c6-11ea-9617-f89b334c402b.png)

## Scoring Algorithm

The scoring algorithm leverages forecasted data on the selected restaurant.  The weighted score (scale of 1-10) is a result of a calculation that includes:
-- Day Rank Max: the day ranking based on maximum busy-ness of the day, ranges 1-7
-- Peak Hours for the Day: the calculation adjusts scoring based on whether the current time is within peak hours, whether it the peak start, peak end, or peak maxium.
-- Surge Traffic for the Day: whether the current time is during a surge period when the most traffic either enters or leaves the restaurant
-- Crowdedness Intensity Score: included is an adjustment based on an intensity score for the current traffic and crowdedness based on -2 (low), -1 (below average), 0 (average), 1 (high), and 2 (very high).  Intensity score is provided by BestTime.io's API, but it is only one component of our weighted safety score.
-- Adjustment weighting: We also apply a slight calculation adjustment to reform the resulting safety score to fall within 0 - 10.  The score can be seen in the colored box value on the details page for Safety Rating.

![Image of Details View](https://user-images.githubusercontent.com/31550856/87201144-03bc2400-c2cc-11ea-860b-39e807d67b05.png)



## Reviewing the Deployed Application

You can review the deployed application at [https://srmchartroom.github.io/Informed-Diner/index.html](https://srmchartroom.github.io/Informed-Diner/index.html).

## Known Bugs & Planned Future Enhancements
  - 1. Currently the profile and account creation simply direct into the listings.  Future enhancement is to create profile creation, tokens, and store those values.
  - 2. The entry and APIs will be refreshed soon which have a cap and will be discontinued.  We plan to provide an entry state for development review to use that will take user input of public and private API keys so that can test the application based on their own APIs.
  - 3. The left slide drawer menu current provides sort and filter controls that are not hooked up to the results, which was deprioritized for the 1.0 MVP release.  Future enhancement is planned to attach these controls.
  - 4. There is currently a loop mismatch bug in the listings that is displaying the incorrect image for the API returned results after storing in the session object, pushing to Firebase and referencing from Firebase.  We'll get that fixed in the next release.
  - 5. the Safety score in the listings is current placeholder only.  This will be updated to display an actual safety score is one exists in a future enhancement release.
  - 6. We hoped to hook into the directions maps api to provide turn by turn directions from the users current geolocation or address based on the stored values in their profile.  Once the profile is component is finally hooked up, we'll look into provided that feature.  
  - 7. The top FAB button that provides the utility functions has some future enhancements planned.  The search button will be hooked into a user entry field that allows the user to search on restaurant name.  The geo button will allow the user to enter/search on a different location (and likely recall previously searched locations).  Favorites button is planned to return results of favorited restaurants.  And the profile button is planned to navigate the user to a profile update page/module.
  - 8. The busiest time and safest times displayed on the restaurant details page carry a couple bugs that are due to be fixed in the next enhancement.  Currently times with half-hours (e.g. 4:30 PM) are showing as .5 (e.g. 4.5PM).  Also, if the API doesn't return safest time values reliably for the given page, the page may display [object Undefined], which will be fixed in the next update.
  - 9. The map is currently a placeholder on the restaurant details page and is planned to be updated to be accurant to the address in the next enhancement release.
  - 10.  A handful of helpful graph and mapping functions are planned leveraging echarts.js.  That exciting enhancement is also planned for the next release.

Be sure to star and watch the repo for updates!
