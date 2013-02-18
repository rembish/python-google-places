python-google-places
=======================

.. _introduction:

**python-google-places** provides a simple wrapper around the experimental
Google Places API.


Installation
-----------------

.. _installation:

pip install https://github.com/slimkrazy/python-google-places/zipball/master

OR

pip install python-google-places

Download source and then:
python setup.py install


Prerequisites
-----------------
.. _prerequisites:

A Google API key with Places activated against it. Please check the Google API
console, here: http://code.google.com/apis/console


Usage
------

.. _usage:

Code is easier to understand than words, so let us dive right in ::


    from googleplaces import GooglePlaces, types, lang

    YOUR_API_KEY = 'AIzaSyAiFpFd85eMtfbvmVNEYuNds5TEF9FjIPI'

    google_places = GooglePlaces(YOUR_API_KEY)

    query_result = google_places.query(
            location='London, England', keyword='Fish and Chips',
            radius=20000, types=[types.TYPE_FOOD])

    if query_result.has_attributions:
        print query_result.html_attributions


    for place in query_result.places:
        # Returned places from a query are place summaries.
        print place.name
        print place.geo_location
        print place.reference

        # The following method has to make a further API call.
        place.get_details()
        # Referencing any of the attributes below, prior to making a call to
        # get_details() will raise a googleplaces.GooglePlacesAttributeError.
        print place.details # A dict matching the JSON response from Google.
        print place.local_phone_number
        print place.international_phone_number
        print place.website
        print place.url


    # Adding and deleting a place
    try:
        added_place = google_places.add_place(name='Mom and Pop local store',
                lat_lng={'lat': 51.501984, 'lng': -0.141792},
                accuracy=100,
                types=types.TYPE_HOME_GOODS_STORE,
                language=lang.ENGLISH_GREAT_BRITAIN)
        print added_place.reference # The Google Places reference - Important!
        print added_place.id

        # Delete the place that you've just added.
        google_places.delete_place(added_place.reference)
    except GooglePlacesError as error_detail:
        # You've passed in parameter values that the Places API doesn't like..
        print error_detail


    # Autocompleating user input
    query_result = google_places.autocomplete(
        'Czech Repub', types=types.TYPE_REGIONS
    )

    for place in query_result.places:
        print place.name # Place predicted name
        print place.description # Full place description
        print place.types # Place type information (i.e. country, or city, or geocode)


Reference
----------

::

    googleplaces.GooglePlacesError
    googleplaces.GooglePlacesAttributeError


    googleplaces.geocode_location(location, sensor=False)
      Converts a human-readable location to a Dict containing the keys: lat, lng.
      Raises googleplaces.GooglePlacesError if the geocoder fails to find the
      specified location.


    googleplaces.GooglePlaces
      query(**kwargs)
        Returns googleplaces.GooglePlacesSearchResult
          kwargs:
            keyword  -- A term to be matched against all available fields, including but
                        not limited to name, type, and address (default None)

            location -- A human readable location, e.g 'London, England' (default None)

            language -- The language code, indicating in which language the results
                        should be returned, if possble. (default en)

            lat_lng  -- A dict containing the following keys: lat, lng (default None)

            name     -- A term to be matched against the names of the Places.
                        Results will be restricted to those containing the passed name value. (default None)

            radius   -- The radius (in meters) around the location/lat_lng to restrict
                        the search to. The maximum is 50000 meters (default 3200)

            rankby   -- Specifies the order in which results are listed:
                        'prominence' (default) or 'distance' (imply no radius argument)

            sensor   -- Indicates whether or not the Place request came from a device
                        using a location sensor (default False)

            types    -- An optional list of types, restricting the results to Places (default [])

      autocomplete(query, **kwargs)
        Returns googleplaces.GooglePlacesAutocompleteResult
          query -- The text string on which to search.
          kwargs:
            language -- The language in which to return results (default lang.ENGLISH)
            country  -- Restricts your results to places within country (default None - no restrictions).
                        Country should be in ISO 3166-1 Alpha-2 form
            lat_lng  -- The point around which you wish to retrieve Place information.
                        A dict containing the following keys: lat, lng
                        (default None)
            radius   -- The radius (in meters) around the location/lat_lng to
                        restrict the search to. The maximum is 50000 meters.
                        (default 3200)
            sensor   -- Indicates whether or not the Place request came from a
                        device using a location sensor (default False).
            types    -- The types of Place results to return. (default None - any results)
                        May contain next values: types.TYPE_GEOCODE, types.TYPE_ESTABLISHMENT,
                        types.TYPE_REGIONS, types.TYPE_CITIES


      get_place(reference)
        Returns a detailed instance of googleplaces.Place


      checkin(reference, sensor=False)
        Checks in an anonymous user in to the Place that matches the reference.
          kwargs:
            reference   -- The unique Google reference for the required place.

            sensor      -- Boolean flag denoting if the location came from a device
                           using its location sensor (default False).


      add_place(**kwargs)
        Returns a dict containing the following keys: reference, id.
          kwargs:
            name        -- The full text name of the Place. Limited to 255
                           characters.

            lat_lng     -- A dict containing the following keys: lat, lng.

            accuracy    -- The accuracy of the location signal on which this request
                           is based, expressed in meters.

            types       -- The category in which this Place belongs. Only one type
                           can currently be specified for a Place. A string or
                           single element list may be passed in.

            language    -- The language in which the Place's name is being reported.
                           (default googleplaces.lang.ENGLISH).

            sensor      -- Boolean flag denoting if the location came from a device
                           using its location sensor (default False).


      delete_place(reference, sensor=False)
        Deletes a place from the Google Places database.
          kwargs:
            reference   -- The unique Google reference for the required place.

            sensor      -- Boolean flag denoting if the location came from a 
                           device using its location sensor (default False).


    googleplaces.GooglePlacesSearchResult
      places
        A list of summary googleplaces.Place instances.

      has_attributions()
        Returns a flag indicating if the search result has html attributions that
        must be displayed.

      html_attributions()
        Returns a List of String html attributions that must be displayed along with
        the search results.

    googleplaces.GooglePlacesAutocompleteResult
      places
        A list of googleplaces.Prediction instances.


    googleplaces.Place
      reference
        Returns a unique identifier for the Place that can be used to fetch full
        details about it. It is recommended that stored references for Places be
        regularly updated. A Place may have many valid reference tokens.

      id
        Returns a unique stable identifier denoting this Place. This identifier
        may not be used to retrieve information about this Place, but can be used 
        to consolidate data about this Place, and to verify the identity of a 
        Place across separate searches

      icon
        contains the URL of a suggested icon which may be displayed to the user when
        indicating this result on a map.

      types
        Returns a List of feature types describing the given result.

      geo_location
        Returns the geocoded latitude,longitude value for this Place.

      name
        Returns the human-readable name for the Place.

      vicinity
        Returns a feature name of a nearby location. Often this feature refers to a
        street or neighborhood.

      rating
        Returns the Place's rating, from 0.0 to 5.0, based on user reviews.

      details
        Returns a Dict representing the full response from the details API request.
        This property will raise a googleplaces.GooglePlacesAttributeError if it is
        referenced prior to get_details()

      formatted_address
        Returns a string containing the human-readable address of this place. Often
        this address is equivalent to the "postal address".
        This property will raise a googleplaces.GooglePlacesAttributeError if it is
        referenced prior to get_details()

      local_phone_number
        Returns the Place's phone number in its local format.
        This property will raise a googleplaces.GooglePlacesAttributeError if it is
        referenced prior to get_details()

      international_phone_number
        Returns the Place's phone number in international format. International
        format includes the country code, and is prefixed with the plus (+) sign.
        This property will raise a googleplaces.GooglePlacesAttributeError if it is
        referenced prior to get_details()

      website
        Returns the authoritative website for this Place, such as a business'
        homepage.

      url
        Returns the official Google Place Page URL of this Place.

      has_attributions
        Returns a flag indicating if the search result has html attributions that
        must be displayed. along side the detailed query result.

      html_attributions
        Returns a List of String html attributions that must be displayed along with
        the detailed query result.

      checkin()
        Checks in an anonynomous user in.

      get_details()
        Retrieves full information on the place matching the reference.


    googleplaces.Prediction
      id
        contains a unique stable identifier denoting this place. This identifier may not be
        used to retrieve information about this place, but can be used to consolidate data
        about this Place, and to verify the identity of a Place across separate searches.

      description
        contains the human-readable name for the returned result. For establishment results,
        this is usually the business name.

      terms
        contains an array of terms identifying each section of the returned description (a
        section of the description is generally terminated with a comma). Each entry in the
        array has a value field, containing the text of the term, and an offset field, defining
        the start position of this term in the description, measured in Unicode characters.

      matched
        contains an offset value and a length. These describe the location of the entered term
        in the prediction result text, so that the term can be highlighted if desired.

      reference
        contains a unique token that you can use to retrieve additional information about this
        place in a Place Details request. You can store this token and use it at any time in
        future to refresh cached data about this Place, but the same token is not guaranteed
        to be returned for any given Place across different searches.

      get_details()
        Retrieves full information on the place matching the reference.
