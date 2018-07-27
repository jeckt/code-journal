Web Static Files
================

### Bootstrap Installation

Create `static` folder inside the Django app folder

```shell
$ wget https://github.com/twbs/bootstrap/releases/download/v3.3.7/bootstrap-3.3.7-dist.zip
$ unzip bootstrap-3.3.7-dist.zip
$ mkdir APP/static
$ mv bootstrap-3.3.7-dist APP/static/bootstrap
$ rm bootstrap-3.3.7-dist.zip
```

Once that's done make sure you add the Bootstrap header into your html.

```html
<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>wallets</title>
  <link href="/static/bootstrap/css/bootstrap.min.css" rel="stylesheet">
</head>
```
 
