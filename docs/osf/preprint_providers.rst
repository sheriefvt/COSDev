==============================
How to add a preprint provider
==============================

Requirements
============
New preprint providers must have the following:

* A name
* A square logo
* A rectangular/wide logo
* 3 unique colors
* A list of acceptable subjects (taxonomies)
* A list of acceptable licenses 
* Emails for:

  * Contact (defaults to ``contact+provider_name@osf.io``)
  * Support (defaults to ``support+provider_name@osf.io``)

Recommended:

* A Twitter_ account
* A Facebook_ Page
* An Instagram_ account

.. _Twitter: https://twitter.com/signup
.. _Facebook: https://www.facebook.com/business/products/pages
.. _Instagram: https://business.instagram.com/gettingstarted/

Creating a new Preprint Provider
================================
1. Update the Populate Preprint Providers script
2. Format and save images to the in the ember-preprints assets directory
3. Add the config entry
4. Create the stylesheet

osf.io Updates
--------------
Create a new feature branch in the **osf.io** repository ``git checkout develop && git checkout -b feature/add-preprint-provider-provider_id``


Updating the OSF populate_preprint_providers script
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You'll need to add a entry to the ``PREPRINT_PROVIDERS`` list in ``scripts/populate_preprint_providers.py``

.. code-block:: python

    {
        '_id': 'provider_id',
        'name': 'provider_name',
        'logo_name': 'provider_id-logo.png',
        'description': 'The description of the preprint provider',
        'banner_name': 'provider_id-banner.png',
        'external_url': 'http://provider-url.org',
        'example': 'abc12', # An example guid for this provider (Will have to be updated after the provider is up)
        # Advisory board should be valid html string in triple quotes
        'advisory_board': '''
            <div class="col-xs-12">
                <h2>Advisory Board</h2>
                <p class="m-b-lg">This is an example advisory board. The members are:</p>
            </div>
            <div class="col-xs-6">
                <ul>
                    <li>Pat Johnson, works at a place</li>
                    <li>Cody Jackson, with a description</li>
                </ul>
            </div>
            <div class="col-xs-6">
                <ul>
                    <li><a href="http://www.example.com/bobsmith">Bob Smith</a>, Awesome board member</li>
                    <li><a href="http://www.example.com/janesmith">Jane Smith</a>, Great board member</li>
                </ul>
            </div>
        ''',
        'email_contact': 'contact+provider_id@osf.io',
        'email_support': 'support+provider_id@osf.io',
        'social_twitter': 'provider_tweets', # optional
        'social_facebook': 'provider_fb', # optional
        'social_instagram': 'provider_ig', # optional
        'licenses_acceptable': ['CC0 1.0 Universal', 'CC-By Attribution 4.0 International', 'No license'], # Determined by the provider
        'header_text': '', # Unused
        'subjects_acceptable':[
            (['Subject', 'Heirarchy'], True), # Tuple<List<String>, Boolean> The list should be the heirarchy of the subjects/taxonomies
            (['Subject2', 'Subject3', 'Subject4'], False),
        ],
    },

Run the script locally to ensure that it works: ``python -m scripts.populate_preprint_providers``. If it's working, commit the changes

ember-preprints Updates
-----------------------
Create a new feature branch in the **ember-preprints** repository ``git checkout develop && git checkout -b feature/add-preprint-provider-provider_id``

Formatting the images
~~~~~~~~~~~~~~~~~~~~~
In the ember-preprints repository: ``public/assets/img/provider_logos``

You'll need:

* White foreground with transparent background (Used on the main OSF Preprints landing page, under the *Preprint Services* Section)
* Black foreground with transparent background (Used on the OSF Preprints discover page, under *Partner Repositories*)
* Square logo with a transparent background (can have color foreground)
* Rectangular/wide logo with a transparent background (can have color foreground, this can be the white foreground image)
* A *sharing logo* that will be displayed on social media sites (preferably 1500x1500 pixels)

You may need to edit the images to meet the requirements. Use ``brew cask install gimp`` to install gimp or use Pixlr_.

Optimitize the images with Optimizilla_ or a similar service. See the `Google Image Optimization Guide`_

.. _Pixlr: https://pixlr.com/editor/
.. _Optimizilla: http://optimizilla.com/
.. _Google Image Optimization Guide: https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/image-optimization

Adding an entry in the config
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
In ``config/environment.js``, There will be a ``PREPRINTS`` object, and a ``providers`` array. You will need to add another
object to that ``providers`` array.

.. code-block:: javascript

    {
        id: 'provider_id' // This must match the ID in the OSF Repo
        logoSharing: { // T
            path: '/assets/img/provider_logos/provider_id-sharing.png', // The path to the provider's sharing logo
            type: 'image/png', // The mime type of the image
            width: 1500, // minimum 200, 1500 preferred (this is the width of the image, in pixels)
            height: 1500 // minimum 200, 1500 preferred (this is the height of the image, in pixels)
        }
    }

Creating the stylesheet
~~~~~~~~~~~~~~~~~~~~~~~
The basic stylesheet must be named ``app/styles/brands/provider_id.scss`` and contain the following:

.. code-block:: scss

    @import 'brand';

    @include brand(
        #ABCDEF,                                // Color, theme color #1 (header backgrounds, hover backgrounds)
        white,                                  // Color, theme color #2 (text color mostly, usually white or black)
        #012345,                                // Color, theme color #3 (navbar color, preferably a dark color)
        #6789AB,                                // Color, theme color #4 (used in link colors)
        black,                                  // Color, theme color #5 (text color that contrasts with #2, usually black or white)
        $logo-dir + 'engrxiv-small.png',        // String, path to the rectangular provider logo
        $logo-dir + 'engrxiv-square-small.png', // String, path to the square provider logo
        true,                                   // Boolean, whether to use the white share logo or not
        false,                                  // Boolean, whether to use theme color 4 or theme color 2 for the navbar link color
        true                                    // Boolean, whether to use the contracts link color (theme color 4)
    );

    // Add any custom styles or overrides here

You may need to manipulate the colors and options to get them to look good. Avoid overriding styles, if at all possible.

Open Pull Requests
------------------
Open pull requests against osf.io and ember-preprints with your changes. Be sure to cross-reference in the PR description that it requires the other PR.
Add notes for QA that include screenshots of the newly added provider
