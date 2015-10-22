# How to use Google Play Services **LOCATION API** to get current latitude & longitude

1) Add to your `AndroidManifest.xml` file the `ACCESS_COARSE_LOCATION` & `ACCESS_FINE_LOCATION`:

        <manifest xmlns:android="http://schemas.android.com/apk/res/android" package="com.example.appname" >
        
            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        
            <application...

2) Go to `app/build.gradle`file and add the following dependency:

        dependencies {
            //.....
            compile 'com.google.android.gms:play-services-location:8.1.0'
        }
        
3) In your activity implement the following: 
        
        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.api.GoogleApiClient;
        import com.google.android.gms.common.api.GoogleApiClient.ConnectionCallbacks;
        import com.google.android.gms.common.api.GoogleApiClient.OnConnectionFailedListener;
        import com.google.android.gms.location.LocationListener;
        import com.google.android.gms.location.LocationRequest;
        import com.google.android.gms.location.LocationServices;
        import com.google.android.gms.maps.GoogleMap;
        
        public class HomeActivity extends AppCompatActivity implements
                ConnectionCallbacks,
                OnConnectionFailedListener,
                LocationListener {
        
            //Define a request code to send to Google Play services
            private final static int CONNECTION_FAILURE_RESOLUTION_REQUEST = 9000;
            private GoogleApiClient mGoogleApiClient;
            private LocationRequest mLocationRequest;
            private double currentLatitude;
            private double currentLongitude;
        
        
            @Override
            protected void onCreate(Bundle savedInstanceState) {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.activity_home);
        
                mGoogleApiClient = new GoogleApiClient.Builder(this)
                        // The next two lines tell the new client that “this” current class will handle connection stuff
                        .addConnectionCallbacks(this)
                        .addOnConnectionFailedListener(this)
                        //fourth line adds the LocationServices API endpoint from GooglePlayServices
                        .addApi(LocationServices.API)
                        .build();
        
                // Create the LocationRequest object
                mLocationRequest = LocationRequest.create()
                        .setPriority(LocationRequest.PRIORITY_HIGH_ACCURACY)
                        .setInterval(10 * 1000)        // 10 seconds, in milliseconds
                        .setFastestInterval(1 * 1000); // 1 second, in milliseconds
        
            }
        
            @Override
            protected void onResume() {
                super.onResume();
                //Now lets connect to the API
                mGoogleApiClient.connect();
            }
            
            @Override
            protected void onPause() {
                super.onPause();
                Log.v(this.getClass().getSimpleName(), "onPause()");
        
                //Disconnect from API onPause()
                if (mGoogleApiClient.isConnected()) {
                    LocationServices.FusedLocationApi.removeLocationUpdates(mGoogleApiClient, this);
                    mGoogleApiClient.disconnect();
                }
        
        
            }
        
            /**
             * If connected get lat and long
             * 
             */
            @Override
            public void onConnected(Bundle bundle) {
                Location location = LocationServices.FusedLocationApi.getLastLocation(mGoogleApiClient);
        
                if (location == null) {
                    LocationServices.FusedLocationApi.requestLocationUpdates(mGoogleApiClient, mLocationRequest, this);
        
                } else {
                    //If everything went fine lets get latitude and longitude
                    currentLatitude = location.getLatitude();
                    currentLongitude = location.getLongitude();
        
                    Toast.makeText(this, currentLatitude + " WORKS " + currentLongitude + "", Toast.LENGTH_LONG).show();
                }
            }
        
        
            @Override
            public void onConnectionSuspended(int i) {}
        
            @Override
            public void onConnectionFailed(ConnectionResult connectionResult) {
                /*
                 * Google Play services can resolve some errors it detects.
                 * If the error has a resolution, try sending an Intent to
                 * start a Google Play services activity that can resolve
                 * error.
                 */
                if (connectionResult.hasResolution()) {
                    try {
                        // Start an Activity that tries to resolve the error
                        connectionResult.startResolutionForResult(this, CONNECTION_FAILURE_RESOLUTION_REQUEST);
                        /*
                         * Thrown if Google Play services canceled the original
                         * PendingIntent
                         */
                    } catch (IntentSender.SendIntentException e) {
                        // Log the error
                        e.printStackTrace();
                    }
                } else {
                    /*
                     * If no resolution is available, display a dialog to the
                     * user with the error.
                     */
                    Log.e("Error", "Location services connection failed with code " + connectionResult.getErrorCode());
                }
            }
        
            /**
             * If locationChanges change lat and long
             * 
             * 
             * @param location
             */
            @Override
            public void onLocationChanged(Location location) {
                currentLatitude = location.getLatitude();
                currentLongitude = location.getLongitude();
        
                Toast.makeText(this, currentLatitude + " WORKS " + currentLongitude + "", Toast.LENGTH_LONG).show();
            }
        
        }

#### If you need more info just go to:
[The Beginner’s Guide to Location in Android](http://blog.teamtreehouse.com/beginners-guide-location-android)

