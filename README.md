# infodesk

package com.example.elina.layout_sample;

import android.support.v7.app.ActionBarActivity;
import android.os.Bundle;
import android.widget.Button;
import android.view.View.OnClickListener;
import android.app.Activity;
import android.content.ContentUris;
import android.database.Cursor;
import android.net.Uri;
import android.os.Bundle;
import android.provider.CalendarContract;
import android.view.View;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.DigitalClock;
import android.widget.LinearLayout;
import android.widget.TableLayout;
import android.widget.TableRow;
import android.widget.TextView;

import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;
import java.util.List;

public class MainActivity extends Activity
{
    TextView mInfo;
    TextView mEvents;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mInfo = (TextView)findViewById(R.id.info);


        mEvents = (TextView)findViewById(R.id.events);
        Button btnShow = (Button) findViewById(R.id.btn_show);
        btnShow.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                readCalendars("ITC 203");
            }
        });
    }
public void addEvents(List<CalendarEvent> calendarEvents) {

        TableLayout tl = (TableLayout) findViewById(R.id.TableLayout01);
        //izdzēst visas rindas
            tl.removeAllViews();

        //liek notikumus klat
            for (CalendarEvent currentEvent : calendarEvents) {

                TableRow tr = new TableRow(this);

                //laiks
                TextView laiks = new TextView(this);
                laiks.setText(currentEvent.getFormatedTime(true) + " - " + currentEvent.getFormatedTime(false));

                //lekcija, studentu grupa
                TextView lekcija = new TextView(this);
                lekcija.setText(currentEvent.lekcija + ", " + currentEvent.stud_grupa);

                //pasniedzejs
                TextView pasniedzejs = new TextView(this);
                pasniedzejs.setText(currentEvent.pasniedzejs);

                tr.addView(laiks);
                tr.addView(lekcija);
                tr.addView(pasniedzejs);
                tl.addView(tr, new TableLayout.LayoutParams(LinearLayout.LayoutParams.WRAP_CONTENT, LinearLayout.LayoutParams.WRAP_CONTENT));
            }

    }

    private List<CalendarEvent> readCalendars(String calendarName)
    {
        List<CalendarEvent> newEvents = null;
        String[] projection =
                new String[]{
                        CalendarContract.Calendars._ID,
                        CalendarContract.Calendars.NAME,
                        CalendarContract.Calendars.ACCOUNT_NAME,
                        CalendarContract.Calendars.ACCOUNT_TYPE};
        Cursor calCursor =
                getContentResolver().
                        query(CalendarContract.Calendars.CONTENT_URI,
                                projection,
                                CalendarContract.Calendars.VISIBLE + " = 1",
                                null,
                                CalendarContract.Calendars._ID + " ASC");
        long calId = -1;
        String calendarAccount = "";
        if (calCursor.moveToFirst()) {
            do {
                long id = calCursor.getLong(0);
                String calName = calCursor.getString(1);
                if(calName != null && calName.equals(calendarName))
                {
                    calendarAccount = calName + " " + calCursor.getString(2);
                    calId = id;
                    break;
                }
            } while (calCursor.moveToNext());
        }
        mInfo.setText(calendarAccount);
        if(calId != -1)
        {

            Calendar currTime = Calendar.getInstance();
            currTime.add(Calendar.HOUR, -4);
            long begin = currTime.getTimeInMillis(); // starting time in milliseconds
            currTime.add(Calendar.DATE, 30);
            long end = currTime.getTimeInMillis();// ending time in milliseconds
            String[] proj =
                    new String[]{
                            CalendarContract.Instances._ID,
                            CalendarContract.Instances.BEGIN,
                            CalendarContract.Instances.END,
                            CalendarContract.Instances.EVENT_ID,
                            CalendarContract.Instances.DESCRIPTION,
                            CalendarContract.Instances.EVENT_LOCATION,
                            CalendarContract.Instances.TITLE};


            Uri.Builder builder = CalendarContract.Instances.CONTENT_URI.buildUpon();
            ContentUris.appendId(builder, begin);
            ContentUris.appendId(builder, end);

            Cursor cursor = getContentResolver().query(builder.build(),
                    proj, CalendarContract.Instances.CALENDAR_ID + " =" + calId,
                    null, "begin ASC");

            String events = "";
            if (cursor.getCount() > 0)
            {
                if(cursor.moveToFirst())
                {
                    do
                    {
                        long eventId = cursor.getLong(3);
                        events += getEventInfo(eventId,cursor.getLong(1),cursor.getLong(2),cursor.getString(4),cursor.getString(5),cursor.getString(6))
                                + "\n";
                        //pārbauda vai ir kāds notikums. Ja nav tad veido jaunu sarakstu
                        if(newEvents == null){
                            newEvents = new List<CalendarEvent>();
                        }
                        CalendarEvent currentEvent = new CalendarEvent();
                        currentEvent.sLaiks = cursor.getLong(1);
                        currentEvent.bLaiks = cursor.getLong(2);
                        ...
                        //ielikt pārējos lielumus

                        newEvents.add(currentEvent);
                    } while(cursor.moveToNext());
                }
            }

            mEvents.setText(events);

        }
        return newEvents;

    }

    private String getEventInfo(long eventId,long startTime, long endTime,String description, String location,String title)
    {
        String eventInfo = "";
//            long selectedEventId = eventId;
//                    String[] proj =
//                new String[]{
//                        CalendarContract.Events._ID,
//                        CalendarContract.Events.DTSTART,
//                        CalendarContract.Events.DTEND,
//                        CalendarContract.Events.EVENT_LOCATION,
//                        CalendarContract.Events.TITLE};
//            Cursor cursor =
//                    getContentResolver().
//                            query(
//                                    CalendarContract.Events.CONTENT_URI,
//                                    proj,
//                                    CalendarContract.Events._ID + " = ? ",
//                                    new String[]{Long.toString(selectedEventId)},
//                                    null);
//            if (cursor.moveToFirst())
        {
            DateFormat df = new SimpleDateFormat("HH:mm");
            String start = df.format( new Date(startTime));
            String end = df.format( new Date(endTime));
            eventInfo = start + "-" + end + " "+ location + " " + title + " " + description;
        }
        return eventInfo;
    }
}
