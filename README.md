# notihub-docs
API Notihub Documentation Generator (based on [slate](https://github.com/slatedocs/slate))

### Prerequisites

You're going to need:

 - **Linux or macOS** — Windows may work, but is unsupported.
 - **Docker** or:
 - **Ruby, version 2.3.1 or newer**
 - **Bundler** — If Ruby is already installed, but the `bundle` command doesn't work, just run `gem install bundler` in a terminal.

### Getting Started

4. Initialize and start Slate. You can either do this locally, or with Vagrant:

```shell
# Run this to run locally
bundle install
bundle exec middleman server
```

You can now see the docs at http://localhost:4567. Whoa! That was fast!

Now Slate is all set up on your machine, you'll probably want to learn more about [editing Slate markdown](https://github.com/slatedocs/slate/wiki/Markdown-Syntax), or [how to publish your docs](https://github.com/slatedocs/slate/wiki/Deploying-Slate).

If you'd prefer to use Docker, instructions are available [in the wiki](https://github.com/slatedocs/slate/wiki/Using-Slate-in-Docker).

### Deploy

* Commit and Push your changes. 
* Run following command
```shell
./deploy.sh
```