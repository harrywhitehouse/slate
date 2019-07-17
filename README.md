[Official WiKi](https://github.com/lord/slate/wiki)

# Prerequisites

You're going to need:

 - **Linux or macOS** — Windows may work, but is unsupported.
 - **Ruby, version 2.3.1 or newer**
 - **Bundler** — If Ruby is already installed, but the `bundle` command doesn't work, just run `gem install bundler` in a terminal.

# Getting Set Up

```shell
# either run this to run locally
bundle install
bundle exec middleman server

# OR run this to run with vagrant
vagrant up
```

You can now see the docs at http://localhost:4567. Whoa! That was fast!

Now that Slate is all set up on your machine, you'll probably want to learn more about [editing Slate markdown](https://github.com/lord/slate/wiki/Markdown-Syntax), or [how to publish your docs](https://github.com/lord/slate/wiki/Deploying-Slate).

If you'd prefer to use Docker, instructions are available [in the wiki](https://github.com/lord/slate/wiki/Docker).

# Deployment

1. `bundle exec middleman build --clean`
2. Go to [epostage blob storage in Azure](https://portal.azure.com/#@myib.com/resource/subscriptions/b7aba27f-2a6b-4e32-88b7-67483527acf2/resourceGroups/epostagegeneralstorage/providers/Microsoft.Storage/storageAccounts/epostagestorage/overview)
3. Click "Blobs" > "docs"
4. Click the "Upload" link at the top
5. Select your /slate/build/index.html (from step 1)
6. Enable "Overwrite if file already exist" checkbox
7. Click the "Upload" button

# Clear CDN cache

1. Access the Azure Portal: https://portal.azure.com
2. Select 'All Resources' from the left side menu dock
3. In the 'Filter by name…' box, type: epostagewebsitedocsProfile
4. Click on the epostagewebsitedocsProfile which is of type 'CDN Profile'
5. On the Overview tab, select Purge
6. On the Purge page, select the Endpoint: myibservicesdocCDN.azureedge.net
7. To purge the entire cache, select Purge all check box, and then Purge button
8. Or to purge a specific path, enter path i.e. /help/chapter1 and then Purge button
9. The pages/site will be reloaded to the cache upon next access. This could take a few minutes to clear.
