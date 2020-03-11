# Heroku
## Setup
Install heroku cli
```
sudo snap install heroku --classic
```
Login (require browser)
```
heroku login
```
## Working with python app
??
In app folder create heroku application. This will create git remote called `heroku`. New generated app name will be visible in console log.
```
heroku create
```
Deploy app to cloud.
```
git push heroku master
```
To check is application running call `ps`.
```
heroku ps
```
To open app call `open`
```
heroku open
```
To check logs.
```
heroku logs
heroku logs --tail
```
### Run app local
Install dependencies
```
pip install -r requirements.txt
```
To run application on local machine using heroku:
```
heroku local web
```
