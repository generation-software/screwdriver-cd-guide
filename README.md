Screwdriver Documentation
=========================

![Netlify](https://img.shields.io/netlify/4cf389eb-91ee-41f0-a7d3-dfadf72cb598?style=for-the-badge&logo=netlify&logoColor=white&labelColor=00C7B7)

> Documentation for the Screwdriver CD service

Screwdriver is a self-contained, pluggable service to help you build, test, and continuously deliver software using the 
latest containerization technologies.

To start using Screwdriver
--------------------------

For more information about Screwdriver, check out our [homepage](https://screwdriver-docs.netlify.app).

To start contributing to Screwdriver
------------------------------------

Have a look at our guidelines, as well as pointers on where to start making changes, in our [contributing guide](http://docs.screwdriver.cd/about/contributing).

The guide is powered by Jekyll. There are two ways to run Jekyll: via Docker and via installation.

### Running Jekyll using Docker

1. Install [docker-desktop](https://www.docker.com/products/docker-desktop) if you haven't already.
2. Ensure Docker is running with `docker info`; if not, then on Mac, you can launch easily using `open -a 
   /Applications/Docker.app/`. Launching on CLI (rather than double-clicking) has the advantage of exporting your 
   `$SSH_AUTH_SOCK` and `ssh-agent` will work properly, should you need it at some point.
3. Run the Jekyll Docker image with mount of `$PWD` to its serving location and with `-ti` so `^C` will kill it.

   ```bash
   docker run -v $PWD:/srv/jekyll:rw -p 4080:4000 -it jekyll/jekyll jekyll serve --source docs --destination _site
   ```

### Running Jekyll by installing

In order to install Jekyll you'll need Ruby, the Ruby package manager (RubyGems), and bundle to install and run Jekyll.
You can check if you have these already installed like so:

```bash
$ ruby --version
ruby 2.4.1
$ gem --version
2.6.12
$ bundle --version
Bundler version 1.15.1
```

Jekyll supports Ruby version 2.1 or above.

You can also build and serve the documentation using Docker (see below). If you choose this approach, there is no need 
to install Ruby/bundle/jekyll.

### Standard

To install the `jekyll` using bundle, making sure we're in the same directory as the `Gemfile`.

Install the `jekyll` package using bundler:

```bash
bundle install
```

You should now have the `jekyll` command installed on your system. Run `bundle exec jekyll --version` to check that everything worked okay.

```bash
$ bundle exec jekyll --version
jekyll 3.8.4
```

Viewing Docs Locally
--------------------

There's a single configuration file named `_config.yml`, and a folder named `docs` that will contain our documentation 
source files.

Jekyll comes with a built-in webserver that lets you preview your documentation as you work on it. You can start the 
webserver locally with Jekyll directly.

### Standard

Jekyll comes with a built-in webserver that lets you preview your documentation as you work on it. We start the 
webserver by making sure we're in the same directory as the `docs` folder, and then running the
`bundle exec jekyll serve --source docs --destination _site` command:

```bash
$ bundle exec jekyll serve --source docs --destination _site
Configuration file: docs/_config.yml
            Source: docs
       Destination: _site
 Incremental build: disabled. Enable with --incremental
      Generating...
                    done in 2.251 seconds.
 Auto-regeneration: enabled for 'docs'
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
```

### Docker

If you don't have Ruby installed, you can easily build and view the Screwdriver Guide using Docker. From the root directory
of the repository, execute:

```bash
docker run --rm \
  --volume="$PWD:/srv/jekyll" -p 4000:4000 \
  -it jekyll/jekyll:4.2.0 \
  jekyll serve --source docs --destination _site
```

This may take some time as it must download all gems specified in the Gemfile on every run. If you need to rebuild the
guide frequently, you could simply commit your changes and work from your committed image containing all dependencies.

For example:

```bash
docker run --volume ... --destination _site # be sure to leave off --rm
docker commit $(docker ps -q -a |head -n 1 | awk '{print $1}') cached-jekyll

# will already contain all installed gems: should be much faster!
docker run --rm \
  --volume="$PWD:/srv/jekyll" -p 4000:4000 \
  -it cached-jekyll \
  jekyll serve --source docs --destination _site
```

### Browse your local guide

Once you successfully start the webserver, open up [http://127.0.0.1:4000/](http://127.0.0.1:4000/) in your browser. 
You'll be able to see the index page being displayed.

Adding Docs
-----------

Simply add a new markdown document to the folder hierarchy in `docs`, and add an entry to the tree in
`docs/_data/menu.yaml`

### Troubleshooting

### Adding a New Jekyll Page Shows Up with No CSS

[Jekyll is dying and has bad reputation of not taking users seriously](https://github.com/jekyll/jekyll/issues/5257),
its build script exit with 0 with errors. This stupid behavior gives us the illusion that "build passes but our site
is not working as expected" such as missing CSS like this page:

![Error loading bad-jekyll.png](./bad-jekyll.png)

Please check GitHub Action build log and we will see there was actually error:

![Error loading build-error.png](./build-error.png)

