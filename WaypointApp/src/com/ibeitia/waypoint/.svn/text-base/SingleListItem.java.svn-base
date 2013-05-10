package com.ibeitia.waypoint;

import java.io.File;
import java.util.ArrayList;

import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;

import org.w3c.dom.Document;
import org.w3c.dom.Element;
import org.w3c.dom.Node;
import org.w3c.dom.NodeList;

import android.app.FragmentManager;
import android.content.Context;
import android.content.Intent;
import android.graphics.Color;
import android.location.Location;
import android.location.LocationManager;
import android.os.Bundle;
import android.os.Environment;
import android.util.Log;
import android.widget.TextView;

import com.google.android.gms.maps.CameraUpdateFactory;
import com.google.android.gms.maps.GoogleMap;
import com.google.android.gms.maps.MapFragment;
import com.google.android.gms.maps.model.LatLng;
import com.google.android.gms.maps.model.PolylineOptions;
import com.google.android.maps.MapActivity;
/**
 * 5/9/13
 * @author Inigo Beitia
 * @author Brendan Ritter
 * @author Chloe Eghtebas
 * 
 * A class to represent a detailed view of a hike. It also contains a map so that you can see what the route looks like
 */
public class SingleListItem extends MapActivity{
	private String hikeName;
	private GoogleMap map;
	 /**
	  * Called when this activity is created, essentially creates the button and other widget functionality by tying java code 
	  * to the xml of the widget using the R.java file.
	  * 
	  * savedInstanceState-A Bundle object
	  * represents the saved activity from and arlier state, allowing 
	  * you to go back using the back hardware button
	  */
	@Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        this.setContentView(R.layout.single_list_item_view);
        
        TextView txtHikeName = (TextView) findViewById(R.id.hikename_label);
        
        Intent i = getIntent();
        // getting attached intent data
        this.hikeName = i.getStringExtra("hikename");
        
        txtHikeName.setText(this.hikeName);
        
        //make the map
        FragmentManager fragmentManager = this.getFragmentManager();
        MapFragment mapFragment = (MapFragment) fragmentManager.findFragmentById(R.id.map);//findViewById(R.id.map);
		map = mapFragment.getMap();
		
		LocationManager manager = (LocationManager) getSystemService(Context.LOCATION_SERVICE);
		Location currentLocation = manager.getLastKnownLocation(LocationManager.GPS_PROVIDER);
		
		if(currentLocation != null) {
			// Move the camera instantly to current location with a zoom of 15.
		    map.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(currentLocation.getLatitude(), currentLocation.getLongitude()), 15));
	
		    // Zoom in, animating the camera.
		    map.animateCamera(CameraUpdateFactory.zoomTo(20), 2000, null);
		}
		DrawPath(map);
	}
	/**
	 * Draws a path on the map view from a kml file.
	 * 
	 * mMapView01- A google Map object
	 */
	private void DrawPath(GoogleMap mMapView01) {
		// Get the kml (XML) doc. And parse it to get the coordinates(direction route).
		try {
			File sdcard = Environment.getExternalStorageDirectory();

	        //Get the kml file
	        File file = new File(sdcard,"GPSLogger/" + this.hikeName);
	        
	        DocumentBuilderFactory dbFactory = DocumentBuilderFactory.newInstance();
	    	DocumentBuilder dBuilder = dbFactory.newDocumentBuilder();
	    	Document doc = dBuilder.parse(file);
	    	
	    	Log.d("xxx", "Parsing document...");
	    	
			if (doc.getElementsByTagName("LineString").getLength() > 0) {
				Log.d("xxx", "Placemark found");
				
				NodeList linestrings = doc.getElementsByTagName("LineString");
				Node linestring = linestrings.item(0);
				
				Node coordinates = null;
				
				if(linestring instanceof Element) {
				    Element docElement = (Element)linestring;
				    coordinates = docElement.getElementsByTagName("coordinates").item(0);
				}
				
				String coords = coordinates.getTextContent();
				
				Log.d("xxx", "Parsing coordinates...");
				String[] pairs = coords.split("\\r?\\n");
				
				ArrayList<LatLng> directionPoint = new  ArrayList<LatLng>();
				//Iterates through the points in the file and adds them to the map
				for (int i = 1; i < pairs.length - 1; i++) {
					Log.d("xxx", "starting " + i);
					
					String[] lngLat = pairs[i].split(",");	// lngLat[0]=longitude
															// lngLat[1]=latitude
															// lngLat[2]=height
					
					Double longitude = Double.parseDouble(lngLat[0]);
					Double latitude = Double.parseDouble(lngLat[1]);
					
					directionPoint.add(new LatLng(latitude, longitude));
					
					Log.d("xxx", "finished " + i);
				}
				Log.d("xxx", "bongo ");
				PolylineOptions rectLine = new PolylineOptions().width(3).color(Color.RED);
				Log.d("xxx", "directionPoint size:" + directionPoint.size());
				//linearly interpolates between points to make a path
				for(int i = 0 ; i < directionPoint.size() ; i++) {          
					rectLine.add(directionPoint.get(i));
				}

				mMapView01.addPolyline(rectLine);
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
	}

	@Override
	protected boolean isRouteDisplayed() {
		// TODO Auto-generated method stub
		return false;
	}
}

