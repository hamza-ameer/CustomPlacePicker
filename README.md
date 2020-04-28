# CustomPlacePicker
Make Your Custom Place Picker in Android




1)

Add the following dependencies in you app level gradle file:

    implementation 'com.google.android.gms:play-services-maps:16.1.0'
    implementation 'com.google.android.gms:play-services-places:16.1.0'

------------------------------------------------------------------------------------------------------------

2)

Get Your Google Maps Api Key From Google Cloud Platform.
  
  1) login to your google account and follow this link: https://console.cloud.google.com/google/maps-apis/


-------------------------------------------------------------------------------------------------------------

3)

Add the following permissions and meta-data tags in application and activity tag in your menifest file:

	<uses-permission android:name="android.permission.INTERNET" />
	 <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />

    //add this both in application and activity tag
     <meta-data
                android:name="com.google.android.geo.API_KEY"
                android:value="Your Api Key" />

    //add this in your application tag
    <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />


------------------------------------------------------------------------------------------------------------


4)

Create a Layout Resource File 'custom_confirm_address.xml' and update its code to:

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:padding="10dp"
    android:layout_height="match_parent">
    <TextView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="Use this location?"
    android:textColor="#000"
    android:textSize="18dp"
    ></TextView>
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Use this location?"
        android:id="@+id/myAddress"

        ></TextView>
    <fragment
        class="com.google.android.gms.maps.MapFragment"
        android:layout_width="match_parent"
        android:id="@+id/mapp"
        android:layout_marginTop="20dp"
        android:layout_height="150dp"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:layout_marginTop="30dp"
        android:gravity="right"

        >
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Change Location"
            android:textColor="#000"
            android:layout_marginRight="20dp"
            android:id="@+id/Change"

            ></Button>
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Select"
            android:textColor="#000"
            android:id="@+id/Select"
            ></Button>

    </LinearLayout>

</LinearLayout>

 ---------------------------------------------------------------------------------------------------------


5)

Create a Dialog Fragment Class  'ConfirmAddress.java' and update its code to:


    import android.app.Activity;
    import android.app.Dialog;
    import android.app.Fragment;
    import android.content.Context;
    import android.content.DialogInterface;
    import android.os.Bundle;
    import android.app.DialogFragment;
    import android.util.Log;
    import android.view.LayoutInflater;
    import android.view.View;
    import android.view.ViewGroup;
    import android.view.Window;
    import android.widget.Button;
    import android.widget.TextView;
    import android.widget.Toast;

    import com.google.android.gms.maps.CameraUpdate;
    import com.google.android.gms.maps.CameraUpdateFactory;
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapFragment;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
    import com.google.android.gms.maps.SupportMapFragment;
    import com.google.android.gms.maps.model.LatLng;
    import com.google.android.gms.maps.model.MarkerOptions;

    import org.w3c.dom.Text;

    import java.io.Console;

    public class ConfirmAddress extends DialogFragment implements
        android.view.View.OnClickListener, OnMapReadyCallback {

    public Activity c;
    public Dialog d;
    public Button yes, no;

    private GoogleMap mMap;
    MapView mapView;
    Double Lat;
    Double Long;
    String Address;
    TextView myAddress;
    Button SelectBtn;
    Button ChangeBtn;
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
         Lat = getArguments().getDouble("lat");
         Long = getArguments().getDouble("long");
        Address = getArguments().getString("address");

    }
    MapFragment mapFragment;
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        View v = inflater.inflate(R.layout.custom_confirm_address, container, false);
        myAddress=(TextView)v.findViewById(R.id.myAddress);
        SelectBtn=(Button) v.findViewById(R.id.Select);
        ChangeBtn=(Button) v.findViewById(R.id.Change);



          mapFragment = (MapFragment) getFragmentManager().findFragmentById(R.id.mapp);
        mapFragment.getMapAsync(this);
       // Toast.makeText(getActivity(),mNum,Toast.LENGTH_LONG).show();

        SelectBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
		Toast.makeText(getActivity(),myAddress.getText().toString(),Toast.LENGTH_LONG).show();
                getFragmentManager().beginTransaction().remove(mapFragment).commit();
               dismiss();
            }
        });
        ChangeBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
		  getFragmentManager().beginTransaction().remove(mapFragment).commit();
               dismiss();
            }
        });
        getDialog().setCanceledOnTouchOutside(true);
        return v;

    }

    @Override
    public void onCancel(DialogInterface dialog) {
        super.onCancel(dialog);
        getFragmentManager().beginTransaction().remove(mapFragment).commit();

    }

    @Override
    public void onDismiss(DialogInterface dialog) {
        super.onDismiss(dialog);
       dismiss();
    }

    @Override
    public void onClick(View v) {

    }

    @Override
    public void onMapReady(GoogleMap googleMap) {
        mMap = googleMap;
        myAddress.setText(Address);
        mMap.getUiSettings().setMyLocationButtonEnabled(false);
        MarkerOptions markerOptions = new MarkerOptions();
        markerOptions.position(new LatLng(Lat,Long));

        markerOptions.title(Address);
        mMap.clear();
        CameraUpdate location = CameraUpdateFactory.newLatLngZoom(
                new LatLng(Lat,Long), 16f);
        mMap.animateCamera(location);
        mMap.addMarker(markerOptions);
        Log.d("status","success");
    }

   }


---------------------------------------------------------------------------------------------------------------

6)

Update your activity_main.xml file with the following code.


    <androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity"

    >
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:id="@+id/map"
    tools:context=".MainActivity"
    android:name="com.google.android.gms.maps.SupportMapFragment" />


</androidx.constraintlayout.widget.ConstraintLayout>



------------------------------------------------------------------------------------------------------------


7)

Update the your MainActivity.java file with the following code:

    
     public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        private GoogleMap mMap;
    
    
        Button btn;
        private final static int PLACE_PICKER_REQUEST = 999;
        private final static int LOCATION_REQUEST_CODE = 23;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            SupportMapFragment mapFragment = (SupportMapFragment) getSupportFragmentManager()
                    .findFragmentById(R.id.map);
            mapFragment.getMapAsync(this);
            if ( ContextCompat.checkSelfPermission( this, android.Manifest.permission.ACCESS_COARSE_LOCATION )
                    != PackageManager.PERMISSION_GRANTED ) {
    
                ActivityCompat.requestPermissions( this, new String[] {  android.Manifest.permission.ACCESS_COARSE_LOCATION  },
                        LOCATION_REQUEST_CODE );
            }
        }
    
    
        @Override
        public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
            switch (requestCode) {
                case LOCATION_REQUEST_CODE: {
                    if (grantResults.length > 0
                            && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
    
    
                        mMap.setMyLocationEnabled(true);
                        mMap.setOnMyLocationChangeListener(new GoogleMap.OnMyLocationChangeListener() {
                            @Override
                            public void onMyLocationChange(Location location) {
                                LatLng ltlng=new LatLng(location.getLatitude(),location.getLongitude());
                                CameraUpdate cameraUpdate = CameraUpdateFactory.newLatLngZoom(
                                        ltlng, 16f);
                                mMap.animateCamera(cameraUpdate);
                            }
                        });
                        Location location = mMap.getMyLocation();
    
                        mMap.setOnMapClickListener(new GoogleMap.OnMapClickListener() {
                            @Override
                            public void onMapClick(LatLng latLng) {
                                MarkerOptions markerOptions = new MarkerOptions();
                                markerOptions.position(latLng);
    
                                markerOptions.title(getAddress(latLng));
                                mMap.clear();
                                CameraUpdate location = CameraUpdateFactory.newLatLngZoom(
                                        latLng, 15);
                                mMap.animateCamera(location);
                                mMap.addMarker(markerOptions);
                            }
                        });
    
    
    
                    } else {
                        // permission denied, boo! Disable the
                        // functionality that depends on this permission.
                    }
                    return;
                }
            }
        }
    
        @Override
        public void onMapReady(GoogleMap googleMap) {
            mMap = googleMap;
    
        }
    
    
        private String getAddress(LatLng latLng){
    
            Geocoder geocoder;
            List<Address> addresses;
            geocoder = new Geocoder(this, Locale.getDefault());
    
            try {
                addresses = geocoder.getFromLocation(latLng.latitude, latLng.longitude, 1); // Here 1 represent max location result to returned, by documents it recommended 1 to 5
                String address = addresses.get(0).getAddressLine(0); // If any additional address line present than only, check with max available address lines by getMaxAddressLineIndex()
                String city = addresses.get(0).getLocality();
                String state = addresses.get(0).getAdminArea();
                String country = addresses.get(0).getCountryName();
                String postalCode = addresses.get(0).getPostalCode();
                String knownName = addresses.get(0).getFeatureName();
                FragmentTransaction ft = getFragmentManager().beginTransaction();
                Fragment prev = getFragmentManager().findFragmentByTag("dialog");
                if (prev != null) {
    
                    ft.remove(prev);
                }
                ft.addToBackStack(null);
                DialogFragment dialogFragment = new ConfirmAddress();
    
                Bundle args = new Bundle();
                args.putDouble("lat", latLng.latitude);
                args.putDouble("long", latLng.longitude);
                args.putString("address", address);
                dialogFragment.setArguments(args);
                dialogFragment.show(ft, "dialog");
                return address;
            } catch (IOException e) {
                e.printStackTrace();
                return "No Address Found";
    
            }
    
    
        }
    }
   
-----------------------------------------------------------------------------------------------------------------

8) Test your App and Enjoy :)




