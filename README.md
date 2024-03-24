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

## Test acarsdec
- Test with WAV files
```
chmod +x acarsdec
./acarsdec -f ../test.wav
```

<details>
 
<summary>Output</summary>

```
[#2 (L:-23.0 E:0)  --------------------------------
Mode : E Label : 5V Id : 4 Nak
Aircraft reg: PH-BXR Flight id: KL1681
No: S53A
Reassembly: skipped

[#2 (L:-19.1 E:0)  --------------------------------
Mode : E Label : Q0 Id : 6 Nak
Aircraft reg: LN-DYY Flight id: DY083J
No: S47A
Reassembly: skipped

[#4 (L:-17.4 E:0)  --------------------------------
Mode : 2 Label : Q0 Id : 4 Nak
Aircraft reg: LN-DYY Flight id: DY083J
No: S46A
Reassembly: skipped

[#1 (L: -8.5 E:0)  --------------------------------
Mode : G Label : H1 Id : 3 Nak
Aircraft reg: F-GTAE Flight id: AF7728
No: D65C
Sublabel: DF
Reassembly: out of sequence
00000/V206,05,124,183,02,00,00000/V3XX,XX,XXX,XXX,XXXX/V4XX,XX,XXX,XXX,XXXX/V5XX,XX,XXX,XXX,XXXX/V6XX,XX,XXX,XXX,XXXX/V7044,078,00081,22222222222111/V8042,083,00061,22222222222111/

[#1 (L:-18.6 E:0)  --------------------------------
Mode : x Label : _d Id : A Ack : 5
Aircraft reg: LN-DYY 
Reassembly: skipped

[#3 (L: -4.6 E:0)  --------------------------------
Mode : 2 Label : _d Id : 0 Ack : W
Aircraft reg: G-DBCK Flight id: BA031T
No: S64A
Reassembly: skipped

[#3 (L: -4.3 E:0)  --------------------------------
Mode : E Label : Q0 Id : 9 Nak
Aircraft reg: G-DBCK Flight id: BA031T
No: S63A
Reassembly: skipped
exiting ...
```

</details>


- Run acarsdec with rtlsdr on freq 131.550 Mhz
```
./acarsdec -r 0 131.550
```

<details>
 
<summary>Output</summary>

```
Found Rafael Micro R820T tuner
Setting sample rate: 2.0000 MS/s
Exact sample rate is: 2000000.052982 Hz
```

</details>
