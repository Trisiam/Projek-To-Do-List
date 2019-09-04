# Projek-To-Do-List
This code use Java Language to create App To Do List

//This is MainActivity.class

package dts.kominfo.mobile.latihancatatan.proyek1;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AlertDialog;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.DialogInterface;
import android.content.Intent;
import android.content.pm.PackageManager;
import android.os.Build;
import android.os.Bundle;
import android.os.Environment;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.widget.AdapterView;
import android.widget.ListView;
import android.widget.SimpleAdapter;
import android.widget.Toast;

import java.io.File;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;

import dts.kominfo.mobile.latihancatatan.proyek1.R;

public class MainActivity extends AppCompatActivity {
    public static final int REQUEST_CODE_STORAGE = 100;
    ListView listView;


    public static final  String FILENAME = "Login";
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //Toolbar toolbar  = findViewByID (R.id.toolbar)
        //setSupportActionBar(toolbar)
        getSupportActionBar().setDisplayShowHomeEnabled(true);
        getSupportActionBar().setTitle("Catatan Harian");

        listView = findViewById(R.id.listView);
        listView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {

                Intent intent = new Intent(MainActivity.this, InsertAndViewActiviity.class);
                Map<String, Object> data = (Map<String, Object>)
                        parent.getAdapter().getItem(position);
                                 intent.putExtra("filename", data.get("nama").toString());
                                 Toast.makeText(MainActivity.this,"",Toast.LENGTH_SHORT).show();
                                 Toast.makeText(MainActivity.this, "You clicked "  + data.get("nama"),Toast.LENGTH_SHORT).show();
                    startActivity(intent);
            }
        });

        listView.setOnItemLongClickListener(new AdapterView.OnItemLongClickListener() {
            @Override
            public boolean onItemLongClick(AdapterView<?> parent, View view, int position, long id) {
                Map<String,Object> data = (Map<String, Object>)
                        parent.getAdapter().getItem(position);

                tampilkanDialogKonfirmasiHapusCatatan(data.get("nama").toString());
                return  true;
            }
        });
    }

    @Override
    protected void onResume() {
        super.onResume();
        if (Build.VERSION.SDK_INT >= 23) {
            if (periksalainPenyimpanan()) {
                mengambilListFilePadaFolder();
            }
            } else {
                mengambilListFilePadaFolder();
            }
        }


    public boolean periksalainPenyimpanan () {
        if (Build.VERSION.SDK_INT >= 23) {
            if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE)
            == PackageManager.PERMISSION_GRANTED)  {
                return true;
            } else {
                ActivityCompat.requestPermissions(this, new  String[]{Manifest.permission.WRITE_EXTERNAL_STORAGE},
                    REQUEST_CODE_STORAGE);
                            return  false;
            }
        }else  {
            return  true;
        }
    }


    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);

        switch (requestCode) {
            case REQUEST_CODE_STORAGE:
                if (grantResults[0]  ==  PackageManager.PERMISSION_GRANTED) {
                                                                        mengambilListFilePadaFolder();
                                                         }
                                                                break;
                        }
                }


                void mengambilListFilePadaFolder () {
                        String path  = Environment.getExternalStorageDirectory().toString()  +  "/kominfo.proyek1";
                    File  directory  =  new File(path);

                            if (directory.exists()) {
                                File [] files  =  directory.listFiles();
                                String [] filename  =  new String[files.length];
                                String [] dateCreated  =  new String[files.length];
                                SimpleDateFormat simpleDateFormat  = new SimpleDateFormat("dd MMM YYY HH:mm:ss");
                                ArrayList<Map<String, Object>> itemDataList  = new ArrayList<Map<String, Object>>();

                                for (int i = 0; i < files.length; i ++) {
                                    filename[i] = files [i].getName();
                                    Date lastModeDate  =  new Date(files[i].lastModified());
                                    dateCreated[i] = simpleDateFormat.format(lastModeDate);
                                    Map<String,Object> listItemMap = new HashMap<>();
                                    listItemMap.put("name",filename[i]);
                                    listItemMap.put("date", dateCreated[i]);
                                    itemDataList.add(listItemMap);
                                }

                                SimpleAdapter simpleAdapter = new SimpleAdapter(this, itemDataList, android.R.layout.simple_list_item_2,
                                        new String[] {"name", "date"}, new int[]{android.R.id.text1, android.R.id.text2});
                                    listView.setAdapter(simpleAdapter);
                                    simpleAdapter.notifyDataSetChanged();
                            }
                    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.menu, menu);

        return  true;
    }

    @Override
    public boolean onOptionsItemSelected(@NonNull MenuItem item) {
        switch (item.getItemId()) {
            case R.id.action_tambah:
                    Intent intent  = new Intent(this, InsertAndViewActiviity.class);
                        startActivity(intent);
                        break;
        }
        return super.onOptionsItemSelected(item);
    }

    void tampilkanDialogKonfirmasiHapusCatatan(final String filename)  {
        new AlertDialog.Builder(this)
                .setTitle("Hapus Catatan ini?")
                .setMessage("Apakah Anda yakin ingin menghapus catatan"  + filename+"?")
                .setIcon(android.R.drawable.ic_dialog_alert)
                .setPositiveButton(android.R.string.yes, new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int witchButton) {
                        hapusFile(filename);
                    }
                })
                .setNegativeButton(android.R.string.no, null).show();
    }

    void hapusFile(String filename) {
        String path = Environment.getExternalStorageDirectory().toString()+  "/kominfo.proyek1";
        File file = new File(path, filename);
            if (file.exists()) {
                file.delete();
            }
            mengambilListFilePadaFolder();
    }
}
