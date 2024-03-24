# How To Build acarsdec on MacOS

## Build libacars first
1. Clone libacars repository
```
git clone https://github.com/szpajder/libacars
```
2. Install **CMAKE** and required dependencies for libacars
```
brew install cmake zlib libxml2 jansson
```
3. Build libacars
```
cd libacars
mkdir build
cd build
```
```
cmake ../
make
sudo make install
```

## Build acarsdec
1. Clone acarsdec repository
```
git clone https://github.com/TLeconte/acarsdec
```
2. Install required dependencies for acarsdec
```
brew install libusb libsndfile librtlsdr
````
`This dependency only support RTLSDR devices. If you don't use rtlsdr check the required library in the acarsdec repo page and adjust accordingly.`

3. Create `osx.patch` inside acarsdec directory

`git apply osx.patch`
```diff
diff --git a/acarsdec.c b/acarsdec.c
index e96581d..1c2501d 100644
--- a/acarsdec.c
+++ b/acarsdec.c
@@ -47,6 +47,12 @@ int daily = 0;
 
 int signalExit = 0;
 
+#if __APPLE__
+    #ifndef HOST_NAME_MAX
+        #define HOST_NAME_MAX 255
+    #endif
+#endif
+
 #ifdef HAVE_LIBACARS
 int skip_reassembly = 0;
 #endif
diff --git a/rtl.c b/rtl.c
index a0c7042..6b254d5 100644
--- a/rtl.c
+++ b/rtl.c
@@ -16,6 +16,8 @@
  *   Foundation, Inc., 675 Mass Ave, Cambridge, MA 02139, USA.
  *
  */
+#     define pthread_tryjoin_np(THR, RETVAL)  pthread_detach((THR))
+
 #ifdef WITH_RTL
 
 #define _GNU_SOURCE
```
3. Build acarsdec
```
cd acarsdec
mkdir build
cd build
```
```
export CPATH=/opt/homebrew/include:/usr/local/include
export LIBRARY_PATH=/opt/homebrew/lib:/usr/local/lib
export DYLD_LIBRARY_PATH=/opt/homebrew/lib:/usr/local/lib
```
```
cmake ../ -Drtl=ON
make
sudo make install
```

4. Test acarsdec
```
chmod +x acarsdec
./acarsdec -f ../test.wav
```

5. Run acarsdec with rtlsdr on freq 131.550 Mhz
```
./acarsdec -r 0 131.550
```
