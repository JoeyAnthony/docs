# Flutter

## Keys

### Keys basics
[Official Explanation](https://www.youtube.com/watch?v=kn0EOS-ZiIc&t=60s)

### Other key article
[Source](https://blog.stackademic.com/how-to-flutter-preserve-widget-state-across-the-rebuild-by-the-parent-widget-0b6184023599)

When you want to reparent the widget in the tree while preserving it's state you can use a `GlobalKey`.

# MacOS

## Pod_Runner framework not found
If there is not native swift code or other changes in the ios folder, just delete it and regenerate it with `flutter create --platform ios .`. It could be the ios version was created with an older Flutter version. This command can be used to generate missing files as well if there are changes made in the ios folder.