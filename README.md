# Bakefile
This small tool will help you manage cli recipes in a simple manner, something like a bash only outlook to makefile. This is a tool I use personally in my projects but I also drew inspiration from *Taskfile* and decided to package it and share it with the rest of the world.

The engine of the project is a file called `Bakefile`, it is a bash script, just clone it at the root of your project and you are good to go. An example of a simple `Bakefile` is shown below

```sh
#!/bin/bash
_nodeModulesBin="./node_modules/.bin"
_composerVendorBin="./vendor/bin"
_VERSION="1.0"
PATH="$_nodeModulesBin:$_composerVendorBin:$PATH"

function _banner(){
    cat << "EOF"

   / /_  ____ _/ /_____  / __(_) /__  
  / __ \/ __ `/ //_/ _ \/ /_/ / / _ \
 / /_/ / /_/ / ,< /  __/ __/ / /  __/
/_.___/\__,_/_/|_|\___/_/ /_/_/\___/ 
EOF
    cat << EOF
Version: $_VERSION
User: $(whoami)
PWD: $PWD

EOF
}

function _default {
    start
}

function install {
    echo "install recipe not implemented"
}

function build {
    echo "build recipe not implemented"
}

function start {
    echo "start recipe not implemented"
}



function help {
    echo "$0 <recipe> <args>"
    echo "Recipes:"
    compgen -A function | grep -v '^_' | cat -n
}

TIMEFORMAT=$'\nRecipes completed in %3lR'
_banner
time ${@:-_default}
```

And to run a recipe, do the following:

```sh
$ bake start

   / /_  ____ _/ /_____  / __(_) /__  
  / __ \/ __ `/ //_/ _ \/ /_/ / / _ \
 / /_/ / /_/ / ,< /  __/ __/ / /  __/
/_.___/\__,_/_/|_|\___/_/ /_/_/\___/ 
Version: 1.0
User: someone
PWD: /var/www/html/Bakefile

start recipe not implemented

Recipe completed in 0m0.005s
```    

## Install
To "install", add the following to your `.bashrc` or `.zshrc` (or `.whateverrc`):

    # Quick start with downloading the default Bakefile to your project
    alias bake-init="curl -s https://raw.githubusercontent.com/samcrosoft/Bakefile/main/Bakefile && chmod +x Bakefile"
    
    # Run your recipes like: bake <recipe>
    alias bake=./Bakefile

## Usage
Open your directory and run `bake-init` to add the default Bakefile template to your project directory:

    $ cd my-project
    $ bake-init

Open the `Bakefile` and add your recipes (functions). To run recipes, use `bake`:

```sh
$ bake help 

   / /_  ____ _/ /_____  / __(_) /__  
  / __ \/ __ `/ //_/ _ \/ /_/ / / _ \
 / /_/ / /_/ / ,< /  __/ __/ / /  __/
/_.___/\__,_/_/|_|\___/_/ /_/_/\___/ 
Version: 1.0
User: adebolaolowofela
PWD: /private/var/www/html/personal/github-projects/Bakefile

./Bakefile <recipe> <args>

Recipes:
     1  build
     2  help
     3  install
     4  start

Recipe completed in 0m0.012s
```    

## Techniques

### Private Recipes
Private recipes are recipes(functions) that are not listed when the `help` recipe is baked. A private recipe starts with an underscore `_` e.g

```sh

function _generate_cert {
    ...
}

function start {
    ...
}

function build {

}

```
Now, lets view all recipes

```sh
$ bake help 

   / /_  ____ _/ /_____  / __(_) /__  
  / __ \/ __ `/ //_/ _ \/ /_/ / / _ \
 / /_/ / /_/ / ,< /  __/ __/ / /  __/
/_.___/\__,_/_/|_|\___/_/ /_/_/\___/ 
Version: 1.0
User: someone
PWD: /var/www/html/Bakefile

./Bakefile <recipe> <args>

Recipes:
     1  build
     2  start
```

Note: The `_generate_cert` recipe is missing, this is because it is a private recipe.


### Arguments
Let’s pass some arguments to a recipe. Arguments are accessible to the recipes via the `$1, $2, $n..` variables. Let’s allow us to specify the port of the HTTP server:

```sh
#!/bin/bash

function local-server {
  port="${1:-8000}"
  symfony local:server:start --port=$port
}

...
```

And if we run the `local-server` recipe with a new port:

```sh
$ bake local-server 7070

   / /_  ____ _/ /_____  / __(_) /__  
  / __ \/ __ `/ //_/ _ \/ /_/ / / _ \
 / /_/ / /_/ / ,< /  __/ __/ / /  __/
/_.___/\__,_/_/|_|\___/_/ /_/_/\___/ 
Version: 1.0
User: someone
PWD: /var/www/html/symfony-project
                                                                                                                   
 [WARNING] The local web server is optimized for local development and MUST never be used in a production setup.                                                                                                                         
 [WARNING] Please note that the Symfony CLI only listens on 127.0.0.1 by default since version 5.10.3.                  
           You can use the --allow-all-ip or --listen-ip flags to change this behavior.                                 
[OK] Web server Listening    
    The Web server is using PHP CLI 8.*
    http://127.0.0.1:7070       

[Web Server ] Jan 15 11:05:38 |DEBUG  | PHP    Reloading PHP versions 
[Web Server ] Jan 15 11:05:39 |DEBUG  | PHP    Using PHP version 8.4.2 (from default version in $PATH) 
```