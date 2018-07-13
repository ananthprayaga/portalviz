# Dash Server for BPI/BI apps

## HTTP Routes
* `/admissions/`: Admissions visualization
* `/xtender/`: Xtender visualization

## Dependencies
* cx_Oracle
* python-dotenv
* dash
* dash-renderer
* dash-html-components
* dash-core-components
* plotly

## Installation
### Linux is recommended but Windows works, too. 
#### Developing custom components does not work on Windows.

## Installation

1. Install python 3
    * https://www.python.org/downloads/
    * Make sure to select "add python to path" 
	
* Install dash, https://plot.ly/dash/installation

* https://plot.ly/dash/getting-started#installation

2. Install 32 Bit Oracle Driver
    * Windows Link* http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html
    * Windows Link* https://oracle.github.io/odpi/doc/installation.html#windows

3. Install dependencies
    * pip3 install cx_Oracle
    * pip3 install python-dotenv
    * pip3 install dash==0.20.0
    * pip3 install dash-renderer==0.11.3
    * pip3 install dash-html-components==0.8.0 
    * pip3 install dash-core-components==0.18.1 
    * pip3 install plotly --upgrade

4. Clone the git repo
    * git clone http://155.247.20.20:8080/root/dash-server.git

5. Enter the directory, eg:
    * cd C:\Users\tuf44442\dash-server

6. Initialize the submodules
    * git submodule init

7. Download the submodules
    * git submodule update --recursive

8. Fetch master branch of submodules to make sure they're up to date
    * cd admissionsViz
        * git pull origin master
    * cd deltaChart
        * git pull origin master
    * cd xtenderDash
        * git pull origin master

9. Add environment (.env) files to the admissionsViz and/or xtenderDash folders
    * Admissions variables ODS_DB_USER, ODS_DB_PASSWORD, ODS_DB_URL
    * Xtender variables OTG_DB_USER, OTG_DB_PASSWORD, OTG_DB_URL
    * See example.env file in root directory for example

10. start the app
    * python[3] server.py

	http://localhost:8050/admissions
	
	
Useful links: 
1. Dash/Plotly install instructions -- https://plot.ly/dash/installation 
2. Git docs -- https://git-scm.com/doc
3. Git cheatsheet -- https://www.git-tower.com/blog/git-cheat-sheet/
4. Python docs -- https://docs.python.org/3/

## Files

```
dash-server/ -- Project root
    admissionsViz/ -- git submodule admissions vizualization. Uses deltachart
    deltachart/ -- git submodule dash component for charts that display delta + last 7 day linegraph
    xtenderDash/ -- git submodule Xtender dashboard 
    .gitignore -- Files to ignore when sending to version control
    .gitmodules -- Describes gitmodules 
    app.py -- Instantiate Dash app, set up initial layout and import router
    example.env -- example .env file
    router.py -- Define routes & set their actions
    server.py -- Runs the app
```

## Adding a new visualization
To create a new visualization, create a folder and add it to a git repository. Avoid using dashes (-) in the name of the git repo/folder. 
We use Python3 on the server so ensure you're writing python3 compatible code. 

### Creating a .env file for database credentials
If the app uses database credentials already in use, you can use them. At the moment that includes OTGMGR and ODSMGR. 
The environment variables are OTG_DB_USER, OTG_DB_PASSWORD, OTG_DB_URL and ODS_DB_USER, ODS_DB_PASSWORD, ODS_DB_URL respectively.

Otherwise, create a .env file and prefix your database credentials with all or part of the username. Please update this file to include the environment variables so they're available to other developers and to avoid collisions. 

### Project structure

```
visualizationName/
   .env
   DataHandler.py
   visualizationName.py
   __init__.py
``` 

### Basic imports

```
import dash
import dash_core_components as dcc
import dash_html_components as html
```

### Doing your thing
Render your visualization in a method called renderPage. For example, 

```
def renderPage():
    return html.Div([
        html.P('This is my page')
    ])
```

Use DataHandler.py to connect to the database 

### Setting up project for development & avoiding changes for production
At the beginning of your file, after importing required dependencies, add a conditional statment like so: 

```
if __name__ == '__main__':
    app = dash.Dash()
else:
    from app import app
```

Then, after your code is done, add another conditional like so: 

```
if __name__ == '__main__':
    app.layout = renderPage
    app.run_server(debug=True, host='0.0.0.0', port=8050)
```

### Running project in dev

After adding your conditionals and the renderPage function, you can run your file: `$ python visualizationName.py`

## Incorporating dev app into dash-server project

To incorporate your app into the dash server project, you'll need to fork the dash-server repo, clone the resulting repo onto your machine, add a submodule, update the router, update the readme, test, then do a pull request. 

1. Forking: login to 155.247.20.20:8080 then navigate to 155.247.20.20:8080/root/dash-server 
2. Cloning: `$ git clone 155.247.20.20:8080/username/dash-server.git`
3. Adding submodule `$ git submodule add 155.247.20.20:8080/username/visualizationName.git`
4. Updating the router (router.py display_page): 
    * Add import: `from visualizationName import visualizationName`
    * Add a new conditional: 
      ```
      elif pathname == '/visualizationName':
          return visualizationName.renderPage()
      ```
5. Updating the readme: 
    1. Add your route
    2. Add any new database variables
