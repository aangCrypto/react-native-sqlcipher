# SQLCipher 4.6.1 Android Compatibility Changes

## Summary

Changes made to make `react-native-sqlcipher` compatible with `net.zetetic:sqlcipher-android:4.6.1@aar`.

---

## 1. `platforms/android/build.gradle`

### Updated `androidx.sqlite` version (`2.0.1` → `2.2.0`)

SQLCipher for Android 4.6.1 requires `androidx.sqlite:sqlite:2.2.0` or later. The previous version `2.0.1` was incompatible.

```diff
- implementation "androidx.sqlite:sqlite:2.0.1"
+ implementation "androidx.sqlite:sqlite:2.2.0"
```

### Replaced deprecated `lintOptions` with `lint`

The `lintOptions` block was deprecated in Android Gradle Plugin 8.x (project uses AGP `8.1.0`).

```diff
- lintOptions {
+ lint {
      abortOnError false
  }
```

---

## 2. `platforms/android/src/main/java/com/reactlibrary/sqlcipher/SqlcipherPlugin.java`

### Fixed `SQLiteException` import (line 18)

The `sqlcipher-android` library does not include its own `SQLiteException` class. It uses the standard Android SDK exception instead.

```diff
- import net.zetetic.database.sqlcipher.SQLiteException;
+ import android.database.sqlite.SQLiteException;
```

This fix resolved all 7 compilation errors referencing `SQLiteException` across the file.

### Fixed `openDatabase()` method signature (line 378)

The `sqlcipher-android` library's `openDatabase` method requires 5 parameters: `(path, password, cursorFactory, flags, databaseHook)`. The previous code only passed 4, causing a compilation error. Added `null` for the `SQLiteDatabaseHook` parameter.

```diff
- SQLiteDatabase mydb = SQLiteDatabase.openDatabase(dbfile.getAbsolutePath(), key, null, openFlags);
+ SQLiteDatabase mydb = SQLiteDatabase.openDatabase(dbfile.getAbsolutePath(), key, null, openFlags, null);
```

---

## 3. Created `platforms/android/consumer-rules.pro`

The `build.gradle` referenced `consumer-rules.pro` via `consumerProguardFiles` but the file did not exist. Created it with ProGuard keep rules to prevent R8/ProGuard from stripping SQLCipher classes in release builds.

```proguard
-keep class net.zetetic.database.** { *; }
-dontwarn net.zetetic.database.**
```

---

## References

- [SQLCipher for Android GitHub](https://github.com/sqlcipher/sqlcipher-android)
- [SQLCipher 4.6.1 Release](https://discuss.zetetic.net/t/sqlcipher-4-6-1-release/6599)
- [Upgrade to 4.6.1 for 16KB device support](https://discuss.zetetic.net/t/android-sqlchiper-upgrade-from-4-2-0-to-4-6-1-for-16kb-devices-support/6776)
