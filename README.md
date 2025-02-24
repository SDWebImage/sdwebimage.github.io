# SDWebImage documentation
SDWebImage Documentation https://sdwebimage.github.io

This repository hosts the in source documentation for [SDWebImage](https://github.com/SDWebImage/SDWebImage) and its related projects. You can view this at [sdwebimage.github.io](https://sdwebimage.github.io).

## DocC Generation

The documentation is auto-generated using [DocC](https://developer.apple.com/documentation/docc).

See more:

+ [Meet DocC documentation in Xcode - WWDC21 - Videos](https://developer.apple.com/videos/play/wwdc2021/10166/)
+ [What's new in Swift-DocC - WWDC22 - Videos](https://developer.apple.com/videos/play/wwdc2022/110368/)

#### Install Xcode (14.0 is required for Objective-C)

The Swift-DocC need Swift 5.5+, which is bundled in Xcode 13.0.

However, if you need generate Objective-C API documentation, need Swift 5.7+, which is bundled in Xcode 14.0


#### Modify build setting and build each framwework

To build Static Hosting Documentation, which is the new feature talked in [SwiftForum](https://forums.swift.org/t/support-hosting-docc-archives-in-static-hosting-environments/53572)

you need to pass `--transform-for-static-hosting` to docc command. However it's not available in Xcode's handy build settings, so you need:

Change the framework Xcode Project's build settings like this (use XCConfig syntax). For example, modify `Module-Shared.xcconfig` in SDWebImage.

You can find a `Configs/Docc.xcconfig` under this repo root path:

```
RUN_DOCUMENTATION_COMPILER = YES
DOCC_EXTRACT_SWIFT_INFO_FOR_OBJC_SYMBOLS = YES
DOCC_ENABLE_CXX_SUPPORT = YES
DOCC_EXTRACT_EXTENSION_SYMBOLS = YES
OTHER_DOCC_FLAGS= --transform-for-static-hosting
```

run with Xcodebuild:

```
xcodebuild build -sdk iphoneos -scheme SDWebImage -configuration Release -destination generic/platform=iOS -xcconfig Docc.xcconfig
```

#### Merge the Docc Archive

After building, you will found something like `SDWebImage.doccarchive` in the build log (actually, the `BUILD_DIR` under Xcode DerivedData path)

Then, repeat until all of frameworks doccarchive built.


Merge them into single one

```
xcrun docc merge SDWebImage.doccarchive SDWebImageSwiftUI.doccarchive XXX.doccarchive -o Output.doccarchive
```

#### Upgrade the documentation

After the `Output.doccarchive` generated, copy and override all the contents of `Output.doccarchive` to this repo's root path

```
cp -R Output.doccarchive/* $current_repo_path/
```

Then, git reset the `index.html` under repo's root path (this is our home page, see below)

```
git reset HEAD index.html
```f

#### Upgrade/Hack the home page (rarely do)

We has some markdown written home page, but it requires to sync the CSS/JS resources from the docc tools.

If you upgrade the toolchain, be sure to update `index.html` following the steps:

1. `xcrun docc convert --fallback-display-name Documentation --fallback-bundle-identifier com.dailymodtion.documentation --fallback-bundle-version 1 --output-dir Home.doccarchive Home.docc`
2. Preview at `Home.doccarchive` and using Chrome to rendering `localhost://8000/documentation/home`
3. Copy all the rendered HTML into the new `index.html` file, remove all the `<script>` label (or added `type="application/json"` to disable its function)
4. Override this repo's `index.html` file

#### Preview the documentation site

Launch the simple HTTP Server locally and see on Chrome/Safari browser.

```
python3 -m http.server
```

Then use browser to open `localhost:8000`

#### Done

~~~
1. SwiftDocC does not support multiple documentations hosting on the same site.

I modify the original generated js files, and let that `index.json` support the framework namespace structure. See `patches/`.

See related feature request here: [Support DocC references to symbols defined in another module](https://github.com/apple/swift-docc/issues/208)

2. SwiftDocC does not support cross-module reference symbol, unlike Apple's own documentation, like reference `Foundation.Data` symbol from `UIKit.UIImage`

I check the generated js files, though we can merge the final `index.json` in to the large one. However, the `data/${module}.json` contains only the symbol USR for current module, when DocC generate for current module, it does not keep the outer symbol USR, so it can not reference from each other.
~~~

---

## Jazzy (Deprecated)

The documentation is auto-generated using [Jazzy](https://github.com/realm/jazzy).

#### Install jazzy

```
[sudo] gem install jazzy
```

#### Modify the contents

This documentation page now hosting both Core repo and other related framework's documentation. To modify the contents, just using the Markdown format and using jazzy to generate the HTML representation. Or directly modify the `index.html`.

#### Generate the SDWebImage documentation

+ Clone both [SDWebImage](https://github.com/SDWebImage/SDWebImage.git), [jazzy-theme](https://github.com/SDWebImage/jazzy-theme.git) and this repo
+ Place them in the same folder
+ Go the SDWebImage folder
+ Run the following command, remember to change the version string:

```
jazzy \
  --objc \
  --author SDWebImage \
  --github_url https://github.com/SDWebImage/SDWebImage \
  --github-file-prefix https://github.com/SDWebImage/SDWebImage/tree/5.9.0 \
  --module-version 5.9.0 \
  --umbrella-header WebImage/SDWebImage.h \
  --documentation=Docs/\*.md \
  --undocumented-text "" \
  --module SDWebImage \
  --framework-root . \
  --sdk iphonesimulator \
  --output ../sdwebimage.github.io/SDWebImage \
  --theme ../jazzy-theme/themes/apple
```

#### Generate SDWebImage related project documentation

SDWebImage now contains many related project, like the [Coder Plugins](https://github.com/SDWebImage/SDWebImage/wiki/Coder-Plugin-List) repo, the SwiftUI repo [SDWebImageSwiftUI](https://github.com/SDWebImage/SDWebImageSwiftUI).

You should place the generated documentation output folder, inside the sub-directory of this repo.

##### Swift

For Swift project, take SDWebImageSwiftUI for example:

```
jazzy \
  --author SDWebImageSwiftUI \
  --github_url https://github.com/SDWebImage/SDWebImageSwiftUI \
  --github-file-prefix https://github.com/SDWebImage/SDWebImageSwiftUI/tree/1.5.0 \
  --module-version 1.5.0 \
  --undocumented-text "" \
  --module SDWebImageSwiftUI \
  --framework-root . \
  --sdk iphonesimulator \
  --output ../sdwebimage.github.io/SDWebImageSwiftUI \
  --theme ../jazzy-theme/themes/apple
```

##### Objective-C

For Objective-C project, it's a little trick that you have to **fake** an umbrella header, make the include header search path, the same as folder structure. Take SDWebImagePhotosPlugin for example:

Move the `SDWebImagePhotosPlugin/Module/SDWebImagePhotosPlugin.h` -> `SDWebImagePhotosPlugin/SDWebImagePhotosPlugin.h`. Then modify all the include form, from this:

```objectivec
#import <SDWebImagePhotosPlugin/NSURL+SDWebImagePhotosPlugin.h>
```

to this:

```objectivec
#import "Classes/NSURL+SDWebImagePhotosPlugin.h"
// And, if there are no `#import <UIKit/UIKit.h>`, add this as well
```

Finally, use jazzy to generate the documentation:

```
jazzy \
  --objc \
  --author SDWebImagePhotosPlugin \
  --github_url https://github.com/SDWebImage/SDWebImagePhotosPlugin \
  --github-file-prefix https://github.com/SDWebImage/SDWebImagePhotosPlugin/tree/0.4.0 \
  --module-version 0.4.0 \
  --umbrella-header SDWebImagePhotosPlugin/SDWebImagePhotosPlugin.h \
  --undocumented-text "" \
  --module SDWebImagePhotosPlugin \
  --framework-root . \
  --sdk iphonesimulator \
  --output ../sdwebimage.github.io/SDWebImagePhotosPlugin \
  --theme ../jazzy-theme/themes/apple
```
