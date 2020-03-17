# Flutter知識メモ

## Flutterの情報集め

[Reso Coder](https://resocoder.com/blog/)

[Medium](https://medium.com/)

[filledstacks.com](https://www.filledstacks.com/)

[トップ10のオープンソースフラッターアプリ](https://medium.com/aviabird/top-10-open-source-flutter-apps-997afff4f1b8)

[START FLUTTER](https://startflutter.com/)









## ディレクトリ構成

`core`

​	`models`　データ構造を定義する

​	`services` MVVMのサービスを定義する

`ui`

​	`something_screen` ViewとViewModelを入れる



## APIをJSONファイルで返すスニペット

```dart
import 'package:http/http.dart' as http;
import 'dart:convert';

class NetworkHelper {
  NetworkHelper(this.url);

  String url;

  // return Decoded Json Data
  Future getJsonData() async {

    var data = await getData();
    return jsonDecode(data);
  }

  Future getData() async {
    http.Response response = await http.get(url);
    if (response.statusCode == 200) {
      String data = response.body;
      return data;
    } else {
      print(response.statusCode);
      return null;
    }
  }
}
```



## MVVMアーキテクチャ

[参考](https://medium.com/flutter-community/a-beginners-guide-to-architecting-a-flutter-app-1e9053211a74)

[FlutterでのMVVMの概要](https://medium.com/better-programming/mvvm-in-flutter-edd212fd767a)



### 依存解決

```yaml
dependencies:
	provider_architecture:
```



### ViewModel

```dart
import 'package:flutter/foundation.dart';
class MyScreenViewModel extends ChangeNotifier {
  int _someValue = 0;
  int get someValue => _someValue;
  Future loadData() async {
    // do initialization...
    notifyListeners();
  }
  void doSomething() {
    // do something...
    notifyListeners();
  }
}
```

### View

```dart
import 'package:provider_architecture/provider_architecture.dart';
class MyScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ViewModelProvider<MyScreenViewModel>.withConsumer(
      viewModel: MyScreenViewModel(),
      onModelReady: (model) => model.loadData(),
      builder: (context, model, child) => MyTopWidget(
        // your widget tree
      ),
    );
  }
}
```



## サービスの作り方

[Flutterアプリで作業を行うサービスを作成する](https://medium.com/flutter-community/creating-services-to-do-the-work-in-your-flutter-app-93d6c4aa7697)



### 抽象クラスでサービスを定義する

```dart
abstract class StorageService {
  Future<int> getCounterValue();
  Future<void> saveCounterValue(int value);
}
```

将来的にサービスの方式を変更する際にコードを書き直す必要性がなくなるため、楽になる。



### 抽象サービスクラスを実装する

- Shared_prefrences
- Database
- Firebase



### サービスロケーターを作成する

```yaml
dependencies:
	get_it:
```



`locator.dart` でlib下に作成する



```dart
import 'storage_service_fake.dart';
import 'package:get_it/get_it.dart';
import 'storage_service.dart';

GetIt locator = GetIt.instance;

setupServiceLocator() {
  locator.registerLazySingleton<StorageService>(() => StorageServiceFake());
}
```



- `factory` サービスプロバイダーからタイプのインスタンスをリクエストすると、毎回新しいインスタンスが取得される。Viewを開いた時に新しくする必要があるViewModelを登録するのに適している。
- `Singleton` シングルトンには2種類あります。登録時に実装を提供するか、インスタンスが最初に要求されたときに呼び出されるlamdaを提供する`LazySingleton`です。Locatorは登録済みタイプの単一のインスタンスを保持し、常にそのインスタンスを返します。

`locator.registerFactory<LoginService>(() => LoginService());`

`locator.registerSingleton(UserService());`

`locator.registerLazySingleton<StorageService>(() => StorageServiceFake());`



### サービスロケーターの初期化

```dart
import 'service_locator.dart';

void main() {
	setupServiceLocator();
	runApp(MyApp());
}
```



### サービスを受ける

ViewModelファイルに追加する。

`StorageService _storageService = locator<StorageService>();`





## Flutter Webを使用

[Flutter for Web：ポートフォリオWebサイトの構築](https://medium.com/flutter-community/flutter-for-web-building-a-portfolio-website-3e9865710efe)



`flutter channel master`

`flutter upgrade`

`flutter config --enable-web`



`flutter create <app_name>`

## extensionを使用する

```yaml
environment:
  sdk: ">=2.7.0 <3.0.0"
```



## ダークテーマを実装する

[Dark Theme — Flutterでユーザーを「提供」します。](https://medium.com/flutter-community/provide-your-users-with-dark-theme-flutter-b81580eee287)



```yaml
dependencies:
	provider:
	shared_preferences:
```



`lib/settings.dart`を作成する



```import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';
```



```dart
import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';

/// Class that contains all your app settings
/// Consists only of Theme setting as of now
class Settings extends ChangeNotifier {
  final SharedPreferences sharedPreferences;

  Settings(this.sharedPreferences);

  bool get isDarkMode => sharedPreferences?.getBool("isDarkMode") ?? false;

  void setDarkMode(bool val) {
    sharedPreferences?.setBool("isDarkMode", val);
    notifyListeners();
  }
}
```



`lib/themes.dart`を作成する

```dart
import 'package:flutter/cupertino.dart';
import 'package:flutter/material.dart';

final setLightTheme = _buildLightTheme();
final setDarkTheme = _buildDarkTheme();

ThemeData _buildLightTheme() {
  return ThemeData(
    primarySwatch: Colors.grey,
    primaryColor: Colors.white,
    brightness: Brightness.light,
    backgroundColor: const Color(0xFFE5E5E5),
    accentColor: Colors.black,
    accentIconTheme: IconThemeData(color: Colors.white),
    dividerColor: Colors.white54,
  );
}

ThemeData _buildDarkTheme() {
  return ThemeData(
    primarySwatch: Colors.grey,
    primaryColor: Colors.black,
    brightness: Brightness.dark,
    backgroundColor: const Color(0xFF212121),
    accentColor: Colors.white,
    accentIconTheme: IconThemeData(color: Colors.black),
    dividerColor: Colors.black12,
  );
}
```



`main.dart`に最終的な変更を追加



IconButtonから呼び出し、実際にテーマを変更する関数

```dart
void changeTheme(bool set, BuildContext context) {
  ///Call setDarkMode method inside our Settings ChangeNotifier
  ///class toNotify all the listeners of the change.
  Provider.of<Settings>(context, listen: false).setDarkMode(set);
}
```



```dart
IconButton(
  icon: Icon(Provider.of<Settings>(context).isDarkMode
      ? Icons.brightness_high
      : Icons.brightness_low),
  onPressed: () {
    changeTheme(
        Provider.of<Settings>(context, listen: false).isDarkMode false : true,context);
  },
)
```



themeをProvider経由で受け取るようにする。

```dart
class _MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      debugShowCheckedModeBanner: false,
      theme: Provider.of<Settings>(context).isDarkMode
          ? setDarkTheme
          : setLightTheme,
      home: MyHomePage(title: 'Flutter Demo Home Page'),
    );
  }
}
```



本体保存するようにする。

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return FutureBuilder<SharedPreferences>(
      future: SharedPreferences.getInstance(),
      builder:
 (BuildContext context, AsyncSnapshot<SharedPreferences> snapshot) {
        return ChangeNotifierProvider<Settings>.value(
          value: Settings(snapshot.data),
          child: _MyApp(),
        );
      },
    );
  }
}
```



## Flutter Widget Guide

[Flutter Widget Guide —インデックス](https://medium.com/@annsh/flutter-widget-guide-index-f03141793b09)



## RoutingをAuto化する

[Flutter ZERO Boilerplate Router with Auto Route –フラッターナビゲーションチュートリアル](https://resocoder.com/2020/01/10/flutter-zero-boilerplate-router-with-auto-route-flutter-navigation-tutorial/)



