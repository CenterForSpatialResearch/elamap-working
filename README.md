# Queens Language Map

Mapping diversity of languages in Queens, NY based on data from the Endangered Language Alliance, the US Census, and Glottolog.



### Usage
1. Architecture
    This is a Node JS [Heroku](heroku.com) App, using an [Express](https://expressjs.com/) backbone and running a MongoDB instance on MLab, with a map generated by Leaflet tiles. Reference tutorials [1](http://denelius.com/leaflet-node-mongodb/) [2](http://denelius.com/a-leaflet-map-with-node-js-mongodb-and-heroku/)

    Express is a Node framework that provides the logic for all routing to be done by the app. All GETs, POSTs,...etc are handled by Express. 

    The app requests, loads, and displays the map layer, then does the same with the database to generate the colored squares that appear as an overlay.

    The admin page for content management makes requests directly to the MongoDB instance via Express.

    *Guide to This Repo:*
    - `views` contains `.jade` files which compile into full html at runtime. `views\admin` are the CMS pages
    - `public\data` contains database dumps, images, and old CSVs, geojsons, and other datasets.
    - `public\javascripts` is the code to run the app. 
        + `public\javascripts\lib` has the libraries that the app relies on 
        + the rest of the `.js` file in this folder perform various functions such as pulling data from MLab, and constructing the cards the pop ups that will appear on the page
        + `public\scripts` has utility nodeJS files. There are also many old CSV and JS files here that are kept for reference. THe relevant files are `insertLanguagesFromCSV.js` and `linkDocumentsFromCSV.js` and are described below.
        + `public\stylesheets`, self explanatory
    - `routes` contains `index.js` which is the backbone of the app. The logic for all the GET and POST requests are handled here.



    
2. Making Changes to App
    For local development: make sure to install npm, node, heroku, and git. On github, fork the repo to your own account.
    Now you can `git clone` the repo to your local machine. 
    Run `heroku login` with the correct credientials to have push access to the herokuapp.

    `git remote add heroku https://git.heroku.com/beyond-the-census.git` connects the remote herokuapp to your local repository.

    Run `npm install` to install the relevant node packages. This might take a while.

    `npm start` runs a server on port 3000 for local development. 
    You can `git push origin master` to push your changes to your Github account. If you want to push to heroku, you need to `git remote add heroku https://git.heroku.com/beyond-the-census.git`
    When you are ready with committed changes...`git push heroku master` to build the app and see your new changes running on `beyond-the-census.herokuapp.com`. 


3. Updating Database
    Add new entries or update old ones via the [admin page](http://beyond-the-census.herokuapp.com/admin)
    Very important to be precise with your entries! Language name needs to match, glottocode needs to be correct, Institution name needs to be correct..etc

    Behavior is undefined for invalid data. 

    OR

### TECHNICAL OVERVIEW

**Scripts for Large Uploads**

- Run `node insertLanguagesFromCSV.js` to upload new languages to the databse. The target CSV should be correctly prepared, and should match the format of the languages collection. This script does not have error checking. Line _7_ of this file reads `var sourcepath = 'path_to_file'`.
- You MUST input the path to the file relative to the `scripts` directory that this script is located in.
- Then, run `node linkDocumentsFromCSV.js` with the same CSV file to add the correct object IDs into the new entries in the databse. Line _13_ of this file reads `var sourcepath = 'path_to_file'`.
- You MUST input the path to the file relative to the `scripts` directory that this script is located in.
- These two scripts will upload documents to the languages collection. If you wish to upload documents to a different collection change the variable `targetcollection` (line 13 of `insertLanguagesFromCSV.js` and line 23 of `linkDocumentsFromCSV.js`
- The first script uploads basic JSON objects to MLab. The second script 'links' these documents to other collections in the databse by searching them and adding `$oid` which is a hash representing a document in some collection.
- The input CSV must be formatted EXACTLY as the sample CSV file, `newEntries.csv`, which is located in the scripts folder. 

**Heroku Backups**

_Creating Backups_

Full database:

```bash
mongodump -h ds151530.mlab.com:51530 -d heroku_n7xsssc4 -u <user> -p <password> -o <output directory> 
```

Single Collection:

```bash
mongodump -h ds151530.mlab.com:51530 -d heroku_n7xsssc4 -c <collection> -u <user> -p <password> -o <output directory>  
```

_Restoring Backups_

```bash
mongorestore -h ds151530.mlab.com:51530 -d heroku_n7xsssc4 -u <user> -p <password> <input db directory> 
```

```bash
mongorestore -h ds151530.mlab.com:51530 -d heroku_n7xsssc4 -u <user> -p <password> <input .bson file>
```

**Heroku Manual Manipulation**

- Login to Heroku. Navigate to the beyond-the-census app. Navigate to Overview, click on mLab MongoDB under add-ons. There are 2 databases here (the 'Purple' isntance is meant to be used for testing)
- Ex. _Delete a Languages document_
    + Open the Languages collection
    + Perform a new Search
    + Enter a query in JSON format
```json
{"language":"Algerian Arabic"}
```
or
```json
{"id":"beng1280"}
```
...etc
- You can now make manual modifications to the database. Add, remove, edit. Same thing can be done for any collection in the database.







