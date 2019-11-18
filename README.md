# SDWebImage documentation
SDWebImage Documentation https://sdwebimage.github.io

This repository hosts the in source documentation for [SDWebImage](https://github.com/SDWebImage/SDWebImage) and its related projects. You can view this at [sdwebimage.github.io](https://sdwebimage.github.io).

## Maintenance

The documentation is auto-generated using [Jazzy](https://github.com/realm/jazzy).

#### Install jazzy

```
[sudo] gem install jazzy
```

#### Modify the contents

This documentation page now hosting both Core repo and other related framework's documentation. To modify the contents, just using the Markdown format and using jazzy to generate the HTML representation. Or directly modify the `index.html`.

#### Generate the SDWebImage documentation

+ Clone both SDWebImage and this repo
+ Place them in the same folder
+ Go the SDWebImage folder
+ Firstly, run `jazzy` command once, it will run `xcodebuild`, ignore the error output (Seems jazzy's issue)
+ Next, run the following command, remember to change the version string:

```
jazzy \
  --objc \
  --author SDWebImage \
  --github_url https://github.com/SDWebImage/SDWebImage \
  --github-file-prefix https://github.com/SDWebImage/SDWebImage/tree/5.2.0 \
  --module-version 5.2.0 \
  --umbrella-header WebImage/SDWebImage.h \
  --documentation=Docs/\*.md \
  --undocumented-text "" \
  --module SDWebImage \
  --framework-root . \
  --sdk iphonesimulator \
  --output ../sdwebimage.github.io/SDWebImage
```

#### Generate SDWebImage related project documentation

SDWebImage now contains many related project, like the [Coder Plugins](https://github.com/SDWebImage/SDWebImage/wiki/Coder-Plugin-List) repo, the SwiftUI repo [SDWebImageSwiftUI](https://github.com/SDWebImage/SDWebImageSwiftUI).

You should place the generated documentation output folder, inside the sub-directory of this repo. Take SDWebImageSwiftUI for example:

```
jazzy \
  --author SDWebImage \
  --github_url https://github.com/SDWebImage/SDWebImageSwiftUI \
  --github-file-prefix https://github.com/SDWebImage/SDWebImageSwiftUI/tree/0.8.3 \
  --module-version 0.8.3 \
  --undocumented-text "" \
  --module SDWebImageSwiftUI \
  --framework-root . \
  --sdk iphonesimulator \
  --output ../sdwebimage.github.io/SDWebImageSwiftUI
```