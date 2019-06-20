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
2. Go to [epostage blob storage in Azure](https://portal.azure.com/#blade/Microsoft_Azure_Storage/ContainerMenuBlade/overview/storageAccountId/%2Fsubscriptions%2Fb7aba27f-2a6b-4e32-88b7-67483527acf2%2FresourceGroups%2Fepostagegeneralstorage%2Fproviders%2FMicrosoft.Storage%2FstorageAccounts%2Fepostagestorage/path/docs/etag/%220x8D592EAA766917C%22)
3. Click the "Upload" link at the top
4. Select your /slate/build/index.html (from step 1)
5. Enable "Overwrite if file already exist" checkbox
6. Click the "Upload" button