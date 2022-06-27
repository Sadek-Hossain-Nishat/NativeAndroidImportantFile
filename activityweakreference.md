// xml code

<?xml version="1.0" encoding="utf-8"?>

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity"
    android:gravity="center"
    android:orientation="vertical">



    <ProgressBar
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        style="@style/Widget.AppCompat.ProgressBar.Horizontal"
        android:layout_margin="16dp"
        android:id="@+id/id_progressbar"
        android:visibility="invisible"
        tools:visibility="visible"/>

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Start"
        android:onClick="startAsyncTask"/>

</LinearLayout>

//java code

import android.os.AsyncTask;
import android.os.Bundle;
import android.view.View;
import android.widget.ProgressBar;
import android.widget.Toast;

import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;

import java.lang.ref.WeakReference;
import java.nio.channels.AsynchronousChannelGroup;

public class MainActivity extends AppCompatActivity {
private ProgressBar progressBar;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        progressBar =findViewById(R.id.id_progressbar);




    }


    public void startAsyncTask(View view) {

        ExampleAsyncTask task =new ExampleAsyncTask(this);
        task.execute(10);




    }



    private static class  ExampleAsyncTask extends AsyncTask<Integer,Integer,String>{
        private WeakReference<MainActivity> activityWeakReference;

        ExampleAsyncTask(MainActivity activity){
            activityWeakReference =new WeakReference<MainActivity>(activity);
        }

        @Override
        protected void onPreExecute() {
            super.onPreExecute();
            MainActivity activity =activityWeakReference.get();
            if (activity==null||activity.isFinishing()){
                return;
            }
            activity.progressBar.setVisibility(View.VISIBLE);
        }

        @Override
        protected String doInBackground(Integer... integers) {
            for (int i = 0; i < integers[0]; i++) {
                publishProgress((i*100)/integers[0]);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

            }
            return "Fininshed";
        }

        @Override
        protected void onProgressUpdate(Integer... values) {
            super.onProgressUpdate(values);
            MainActivity activity =activityWeakReference.get();
            if (activity==null||activity.isFinishing()){
                return;
            }
            activity.progressBar.setProgress(values[0]);
        }

        @Override
        protected void onPostExecute(String s) {
            super.onPostExecute(s);
            MainActivity activity =activityWeakReference.get();
            if (activity==null||activity.isFinishing()){
                return;
            }
            Toast.makeText(activity, s, Toast.LENGTH_SHORT).show();
            activity.progressBar.setProgress(0);
            activity.progressBar.setVisibility(View.INVISIBLE);
        }




    }
