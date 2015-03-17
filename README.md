package com.example.elina.layout_sample;

/**
 * Created by ELINA on 2015.03.16..
 */

import java.util.List;
import java.util.Date;
import java.util.Calendar;
import android.text.format.DateFormat;




public class CalendarEvent {
    Date sLaiks;
    Date bLaiks;
    String lekcija;
    String auditorija;
    String stud_grupa;
    String kalendar_nos;
    String pasniedzejs;

public String getFormatedTime (boolean startTime)
    {

        java.text.SimpleDateFormat simpleDateFormat = new java.text.SimpleDateFormat("hh:mm");


        if(startTime){
            return simpleDateFormat.format(sLaiks);
        }else {
            return simpleDateFormat.format(bLaiks);
              }
    }


public String getFormatedDate(boolean startDate){

        java.text.DateFormat dateFormat = android.text.format.DateFormat.getDateFormat("dd.MM.yy.");

    if(startDate){
        return dateFormat.format(sLaiks);
    }else {
        return dateFormat.format(bLaiks);
    }
    }









}

