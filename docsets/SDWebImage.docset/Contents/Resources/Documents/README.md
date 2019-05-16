# SDWebImage documentation
SDWebImage Documentation https://sdwebimage.github.io

This repository hosts the in source documentation for [SDWebImage](https://github.com/SDWebImage/SDWebImage). You can view this at [sdwebimage.github.io](https://sdwebimage.github.io).

## Maintenance

The documentation is auto-generated using [Jazzy](https://github.com/realm/jazzy).

#### Install jazzy

```
[sudo] gem install jazzy
```

#### Generate the SDWebImage documentation

+ Clone both SDWebImage and this repo
+ Place them in the same folder
+ Go the SDWebImage folder
+ Run the following command (remember to update the version number)

```
jazzy \
  --objc \
  --author SDWebImage \
  --github_url https://github.com/SDWebImage/SDWebImage \
  --github-file-prefix https://github.com/SDWebImage/SDWebImage/tree/5.0.0 \
  --module-version 5.0.0 \
  --umbrella-header WebImage/SDWebImage.h \
  --documentation=Docs/\*.md \
  --undocumented-text "" \
  --module SDWebImage \
  --framework-root . \
  --sdk iphonesimulator \
  --output ../sdwebimage.github.io
```

