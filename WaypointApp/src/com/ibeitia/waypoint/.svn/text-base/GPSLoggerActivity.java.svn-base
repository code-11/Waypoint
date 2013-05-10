package com.ibeitia.waypoint;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.FileWriter;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.text.DecimalFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.HashMap;

import com.ibeitia.waypoint.R;

import android.app.Activity;
import android.app.AlertDialog;
import android.content.DialogInterface;
import android.content.Intent;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.EditText;
import android.widget.RadioButton;
import android.widget.Toast;
import android.widget.ToggleButton;
/**
 * 5/9/13
 * @author Inigo Beitia
 * @author Brendan Ritter
 * @author Chloe Eghtebas
 * 
 * This class is the user interface for the gps logging. 
 * This class can start, stop and save to the database
 * the gps information
 */
public class GPSLoggerActivity extends Activity {
	
	private static final String tag = "GPSLoggerActivity";
	
	private static final String tripFileName = "currentTrip.txt";
	
	private String currentTripName = "";
	
	private int altitudeCorrectionMeters = 15;
	
	private final DecimalFormat sevenSigDigits = new DecimalFormat("0.#######");

	/**
	 * This function is called when this activity is initialized.
	 * This function sets up all the listeners and functionality for the page
	 * 
	 * savedInstanceState- A bundle object
	 */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main); 

        Button button = (Button)findViewById(R.id.ButtonStart);
        button.setOnClickListener(mStartListener);
        button = (Button)findViewById(R.id.ButtonStop);
        button.setOnClickListener(mStopListener);
        button = (Button)findViewById(R.id.ButtonExport);
		button.setOnClickListener(mExportListener);
		initTripName();
    }
	/**
	 * Check to see if there is a kml file already on the sdcard
	 * If there is, it load that name to be in the text feild
	 * If there is not, it makes the date the new name of the file.
	 * However, users can overwrite that default
	 */
    private void initTripName() {
		// see if there's currently a trip in the trip file
    	String tripName = "new";
    	try {
	        FileInputStream fIn = openFileInput(tripFileName);
	        InputStreamReader isr = new InputStreamReader(fIn);
	        char[] inputBuffer = new char[1024];
	        isr.read(inputBuffer);
	        isr.close();
	        fIn.close();
	        tripName = new String(inputBuffer).trim();
	        Log.i(tag,"loaded trip name: "+tripName);
    	} catch (FileNotFoundException fnfe) {
    		Log.i(tag,"first run, no "+tripFileName);
    		//If there is not a trip in the trip file, just use the date
    		try {
	    		SimpleDateFormat sdf = new SimpleDateFormat("yyyyMMMdd");
	    		tripName = sdf.format(new Date());
	    		saveTripName(tripName);
    		} catch (Exception e) {
    			Log.e(tag, e.toString());
    		}
    	} catch (IOException ioe) {
    		Log.e(tag, ioe.toString());
    	}
    	//Set the name of the file to the text editor
    	EditText tripNameEditor = (EditText)findViewById(R.id.EditTextTripName);
    	tripNameEditor.setText(tripName);
    	currentTripName = tripName;
	}
    /**
     * Makes a new file with the given name
     * 
     * tripName- The name of the trip
     */
    private void saveTripName(String tripName) throws FileNotFoundException, IOException {
        FileOutputStream fOut = openFileOutput(tripFileName,
                MODE_PRIVATE);
        OutputStreamWriter osw = new OutputStreamWriter(fOut); 
        osw.write(tripName);
        osw.flush();
        osw.close();
        fOut.close();
    }
    /**
     * Note that doNewTripDialog is also called with start. 
     */
	private OnClickListener mStartListener = new OnClickListener() {
        public void onClick(View v)
        {
            startService(new Intent(GPSLoggerActivity.this, GPSLoggerService.class));
            doNewTripDialog();
        }
    };

    private OnClickListener mStopListener = new OnClickListener() {
        public void onClick(View v)
        {
            stopService(new Intent(GPSLoggerActivity.this,
                    GPSLoggerService.class));
        }
    };
    
	private OnClickListener mNewTripListener = new OnClickListener() {
        public void onClick(View v)
        {
        	doNewTripDialog();
        }
    };
    
    private OnClickListener mToggleDebugListener = new OnClickListener() {
		public void onClick(View v) {
			boolean currentDebugState = GPSLoggerService.isShowingDebugToast();
			GPSLoggerService.setShowingDebugToast(!currentDebugState);
			//ToggleButton toggleButton = (ToggleButton)findViewById(R.id.ToggleButtonDebug);
			//toggleButton.setChecked(!currentDebugState);
		}
    };
    /**
     * Creates a dialog, asking the user if they are sure they want to make a new trip file.
     * If they want to, a new database is created and eventually a new file.
     */
    private void doNewTripDialog() {
    	AlertDialog.Builder ad = new AlertDialog.Builder(GPSLoggerActivity.this);
    	ad.setTitle("Whammo!");
    	ad.setMessage("Are you sure that you want to start a new hike?");
    	ad.setPositiveButton("Yes", new DialogInterface.OnClickListener() {
		    	public void onClick(DialogInterface dialog, int whichButton) {
		    		//Starts the process of making a new trip
		    		doNewTrip();
		    	}
    		});
    	ad.setNegativeButton("Cancel", new DialogInterface.OnClickListener() {
    			public void onClick(DialogInterface dialog, int whichButton) {
    				initTripName();
    			}
    		});
    	ad.show();
    }
    /**
     * Handles the creation of a new trip table in the sql database
     */
    private void doNewTrip() {
		SQLiteDatabase db = null;
		try {
			//doExport();
			db = openOrCreateDatabase(GPSLoggerService.DATABASE_NAME, SQLiteDatabase.OPEN_READWRITE, null);
			db.execSQL("DELETE FROM " + GPSLoggerService.POINTS_TABLE_NAME);
	    	EditText tripNameEditor = (EditText)findViewById(R.id.EditTextTripName);
			saveTripName(tripNameEditor.getText().toString());
    	} catch (Exception e) {
    		Log.e(tag, e.toString());
    	} finally {
    		if (db != null && db.isOpen())
    			db.close();
    	}
    }
    
    private OnClickListener mExportListener = new OnClickListener() {
    	public void onClick(View v) {
    		doExport();
    	}
    };
	/**
	 * Exports all the gps points in the database table to a kml file
	 */
	private void doExport() {
		// export the db contents to a kml file
		SQLiteDatabase db = null;
		Cursor cursor = null;
		try {
			altitudeCorrectionMeters = 0;
			Log.i(tag, "altitude Correction updated to "+altitudeCorrectionMeters);
			db = openOrCreateDatabase(GPSLoggerService.DATABASE_NAME, SQLiteDatabase.OPEN_READWRITE, null);
			cursor = db.rawQuery("SELECT * " +
                    " FROM " + GPSLoggerService.POINTS_TABLE_NAME +
                    " ORDER BY GMTTIMESTAMP ASC",
                    null);
			//makes sure all columns are present
            int gmtTimestampColumnIndex = cursor.getColumnIndexOrThrow("GMTTIMESTAMP");
            int latitudeColumnIndex = cursor.getColumnIndexOrThrow("LATITUDE");
            int longitudeColumnIndex = cursor.getColumnIndexOrThrow("LONGITUDE");
            int altitudeColumnIndex = cursor.getColumnIndexOrThrow("ALTITUDE");
            int accuracyColumnIndex = cursor.getColumnIndexOrThrow("ACCURACY");
            //moves through each cell and gets information 
			if (cursor.moveToFirst()) {
				StringBuffer fileBuf = new StringBuffer();
				String beginTimestamp = null;
				String endTimestamp = null;
				String gmtTimestamp = null;
				initFileBuf(fileBuf, initValuesMap());
				do {
					gmtTimestamp = cursor.getString(gmtTimestampColumnIndex);
					if (beginTimestamp == null) {
						beginTimestamp = gmtTimestamp;
					}
					double latitude = cursor.getDouble(latitudeColumnIndex);
					double longitude = cursor.getDouble(longitudeColumnIndex);
					double altitude = cursor.getDouble(altitudeColumnIndex) + altitudeCorrectionMeters;
					double accuracy = cursor.getDouble(accuracyColumnIndex);
					fileBuf.append(sevenSigDigits.format(longitude)+","+sevenSigDigits.format(latitude)+","+altitude+"\n");
				} while (cursor.moveToNext());
				//puts end time stamp on the file
				endTimestamp = gmtTimestamp;
				closeFileBuf(fileBuf, beginTimestamp, endTimestamp);
				String fileContents = fileBuf.toString();
				Log.d(tag, fileContents);
				File sdDir = new File("/sdcard/GPSLogger");
				sdDir.mkdirs();
				//makes a new kml file
				File file = new File("/sdcard/GPSLogger/"+currentTripName+".kml");
				FileWriter sdWriter = new FileWriter(file, false);
				sdWriter.write(fileContents);
				sdWriter.close();
    			Toast.makeText(getBaseContext(),
    					"Export completed!",
    					Toast.LENGTH_LONG).show();
			} else {
				Toast.makeText(getBaseContext(),
						"I didn't find any location points in the database, so no KML file was exported.",
						Toast.LENGTH_LONG).show();
			}
		} catch (FileNotFoundException fnfe) {
			Toast.makeText(getBaseContext(),
					"Error trying access the SD card. Make sure your handset is not connected to a computer and the SD card is properly installed",
					Toast.LENGTH_LONG).show();
		} catch (Exception e) {
			Toast.makeText(getBaseContext(),
					"Error trying to export: " + e.getMessage(),
					Toast.LENGTH_LONG).show();
		} finally {
			if (cursor != null && !cursor.isClosed()) {
				cursor.close();
			}
			if (db != null && db.isOpen()) {
				db.close();
			}
		}
	}
	/**
	 * Makes some intial values that will eventually go into the database
	 */
	private HashMap<String, String> initValuesMap() {
		HashMap<String, String> valuesMap = new HashMap<String, String>();

		valuesMap.put("FILENAME", currentTripName);
		

			// use ground settings for the export
			valuesMap.put("EXTRUDE", "0");
			valuesMap.put("TESSELLATE", "1");
			valuesMap.put("ALTITUDEMODE", "clampToGround");
		
		return valuesMap;
	}
	/**
	 * Automatically generated the header of the kml file.
	 * 
	 * fileBuf-A buffer for the input
	 * valuesMap-A map of some defualt values to populate the header with
	 */
	private void initFileBuf(StringBuffer fileBuf, HashMap<String, String> valuesMap) {
		fileBuf.append("<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n");
		fileBuf.append("<kml xmlns=\"http://www.opengis.net/kml/2.2\">\n");
		fileBuf.append("  <Document>\n");
		fileBuf.append("    <name>"+valuesMap.get("FILENAME")+"</name>\n");
		fileBuf.append("    <description>GPSLogger KML export</description>\n");
		fileBuf.append("    <Style id=\"yellowLineGreenPoly\">\n");
		fileBuf.append("      <LineStyle>\n");
		fileBuf.append("        <color>7f00ffff</color>\n");
		fileBuf.append("        <width>4</width>\n");
		fileBuf.append("      </LineStyle>\n");
		fileBuf.append("      <PolyStyle>\n");
		fileBuf.append("        <color>7f00ff00</color>\n");
		fileBuf.append("      </PolyStyle>\n");
		fileBuf.append("    </Style>\n");
		fileBuf.append("    <Placemark>\n");
		fileBuf.append("      <name>Absolute Extruded</name>\n");
		fileBuf.append("      <description>Transparent green wall with yellow points</description>\n");
		fileBuf.append("      <styleUrl>#yellowLineGreenPoly</styleUrl>\n");
		fileBuf.append("      <LineString>\n");
		fileBuf.append("        <extrude>"+valuesMap.get("EXTRUDE")+"</extrude>\n");
		fileBuf.append("        <tessellate>"+valuesMap.get("TESSELLATE")+"</tessellate>\n");
		fileBuf.append("        <altitudeMode>"+valuesMap.get("ALTITUDEMODE")+"</altitudeMode>\n");
		fileBuf.append("        <coordinates>\n");
	}
	/**
	 * Generates the footer of the file, the body is filled in by the GPS service.
	 * 
	 * fileBuf- A buffer for input
	 * beginTimestamp-When the hike began 
	 * endTimestamp-When the hike ended
	 */
	private void closeFileBuf(StringBuffer fileBuf, String beginTimestamp, String endTimestamp) {
		fileBuf.append("        </coordinates>\n");
		fileBuf.append("     </LineString>\n");
		fileBuf.append("	 <TimeSpan>\n");
		String formattedBeginTimestamp = zuluFormat(beginTimestamp);
		fileBuf.append("		<begin>"+formattedBeginTimestamp+"</begin>\n");
		String formattedEndTimestamp = zuluFormat(endTimestamp);
		fileBuf.append("		<end>"+formattedEndTimestamp+"</end>\n");
		fileBuf.append("	 </TimeSpan>\n");
		fileBuf.append("    </Placemark>\n");
		fileBuf.append("  </Document>\n");
		fileBuf.append("</kml>");
	}
	/**
	 * Some time formatting
	 * 
	 * beginTimestamp- When the hike started
	 */
	private String zuluFormat(String beginTimestamp) {
		// turn 20081215135500 into 2008-12-15T13:55:00Z
		StringBuffer buf = new StringBuffer(beginTimestamp);
		buf.insert(4, '-');
		buf.insert(7, '-');
		buf.insert(10, 'T');
		buf.insert(13, ':');
		buf.insert(16, ':');
		buf.append('Z');
		return buf.toString();
	}

	public void setAltitudeCorrectionMeters(int altitudeCorrectionMeters) {
		this.altitudeCorrectionMeters = altitudeCorrectionMeters;
	}

	public int getAltitudeCorrectionMeters() {
		return altitudeCorrectionMeters;
	}
}
