---
toc: true
title: "Android: Android Permission 체크 및 요청 샘플"
description: "Android Permission 체크 및 요청 샘플" 
categories: [Android]
tags: [Android]
redirect_from:
  - /2020/01/07/
---

> Android Permission 체크 및 요청 샘플

### ContextCompat.checkSelfPermission {#toc1}
```java 
    ArrayList<String> permissions = new ArrayList<String>();
    if (ContextCompat.checkSelfPermission(this, Manifest.permission.CAMERA) != PackageManager.PERMISSION_GRANTED) {
        Log.i("WONY", "CAMERA");
        permissions.add(Manifest.permission.CAMERA);
    }
    if (ContextCompat.checkSelfPermission(this, Manifest.permission.ACCESS_COARSE_LOCATION) != PackageManager.PERMISSION_GRANTED) {
        Log.i("WONY", "ACCESS_COARSE_LOCATION");
        permissions.add(Manifest.permission.ACCESS_COARSE_LOCATION);
    }
    if (ContextCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED) {
        Log.i("WONY", "ACCESS_FINE_LOCATION");
        permissions.add(Manifest.permission.ACCESS_FINE_LOCATION);
    }
    if (ContextCompat.checkSelfPermission(this, Manifest.permission.READ_PHONE_STATE) != PackageManager.PERMISSION_GRANTED) {
        Log.i("WONY", "READ_PHONE_STATE");
        permissions.add(Manifest.permission.READ_PHONE_STATE);
    }
    if (ContextCompat.checkSelfPermission(this, Manifest.permission.RECORD_AUDIO) != PackageManager.PERMISSION_GRANTED) {
        Log.i("WONY", "RECORD_AUDIO");
        permissions.add(Manifest.permission.RECORD_AUDIO);
    }
    if (ContextCompat.checkSelfPermission(this, Manifest.permission.READ_CONTACTS) != PackageManager.PERMISSION_GRANTED) {
        Log.i("WONY", "READ_CONTACTS");
        permissions.add(Manifest.permission.READ_CONTACTS);
    }
    if (ContextCompat.checkSelfPermission(this, Manifest.permission.WRITE_CONTACTS) != PackageManager.PERMISSION_GRANTED) {
        Log.i("WONY", "WRITE_CONTACTS");
        permissions.add(Manifest.permission.WRITE_CONTACTS);
    }
    if (ContextCompat.checkSelfPermission(this, Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED) {
        Log.i("WONY", "WRITE_EXTERNAL_STORAGE");
        permissions.add(Manifest.permission.WRITE_EXTERNAL_STORAGE);
    }
    if (ContextCompat.checkSelfPermission(this, Manifest.permission.CALL_PHONE) != PackageManager.PERMISSION_GRANTED) {
        Log.i("WONY", "CALL_PHONE");
        permissions.add(Manifest.permission.CALL_PHONE);
    }

    if(permissions.size() > 0) {
        String[] reqPermissionArray = new String[permissions.size()];
        reqPermissionArray = permissions.toArray(reqPermissionArray);
        ActivityCompat.requestPermissions(this, reqPermissionArray, REQUEST_PERMISSION_AUTHORITY);
    }else{
        성공로직....
    }
```

### onRequestPermissionsResult {#toc2}
```java
@Override
    public void onRequestPermissionsResult(int requestCode, String permissions[], int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);

        int total = 0;
        switch (requestCode) {
            case REQUEST_PERMISSION_AUTHORITY:
                if (grantResults.length > 0){
                    if (ContextCompat.checkSelfPermission(this, Manifest.permission.CAMERA) == PackageManager.PERMISSION_GRANTED) {
                        Log.i("WONY", "CAMERA GRANTED");
                        total++;
                    }
                    if (ContextCompat.checkSelfPermission(this, Manifest.permission.VIBRATE) == PackageManager.PERMISSION_GRANTED) {
                        Log.i("WONY", "VIBRATE GRANTED");
                        total++;
                    }
                    if (ContextCompat.checkSelfPermission(this, Manifest.permission.ACCESS_COARSE_LOCATION) == PackageManager.PERMISSION_GRANTED) {
                        Log.i("WONY", "ACCESS_COARSE_LOCATION GRANTED");
                        total++;
                    }
                    if (ContextCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) == PackageManager.PERMISSION_GRANTED) {
                        Log.i("WONY", "ACCESS_FINE_LOCATION GRANTED");
                        total++;
                    }
                    if (ContextCompat.checkSelfPermission(this, Manifest.permission.ACCESS_LOCATION_EXTRA_COMMANDS) == PackageManager.PERMISSION_GRANTED) {
                        Log.i("WONY", "ACCESS_LOCATION_EXTRA_COMMANDS GRANTED");
                        total++;
                    }
                    if (ContextCompat.checkSelfPermission(this, Manifest.permission.READ_PHONE_STATE) == PackageManager.PERMISSION_GRANTED) {
                        Log.i("WONY", "READ_PHONE_STATE GRANTED");
                        total++;
                    }
                    if (ContextCompat.checkSelfPermission(this, Manifest.permission.INTERNET) == PackageManager.PERMISSION_GRANTED) {
                        Log.i("WONY", "INTERNET GRANTED");
                        total++;
                    }
                    if (ContextCompat.checkSelfPermission(this, Manifest.permission.RECORD_AUDIO) == PackageManager.PERMISSION_GRANTED) {
                        Log.i("WONY", "RECORD_AUDIO GRANTED");
                        total++;
                    }
                    if (ContextCompat.checkSelfPermission(this, Manifest.permission.MODIFY_AUDIO_SETTINGS) == PackageManager.PERMISSION_GRANTED) {
                        Log.i("WONY", "MODIFY_AUDIO_SETTINGS GRANTED");
                        total++;
                    }
                    if (ContextCompat.checkSelfPermission(this, Manifest.permission.READ_CONTACTS) == PackageManager.PERMISSION_GRANTED) {
                        Log.i("WONY", "READ_CONTACTS GRANTED");
                        total++;
                    }
                    if (ContextCompat.checkSelfPermission(this, Manifest.permission.WRITE_CONTACTS) == PackageManager.PERMISSION_GRANTED) {
                        Log.i("WONY", "WRITE_CONTACTS GRANTED");
                        total++;
                    }
                    if (ContextCompat.checkSelfPermission(this, Manifest.permission.WRITE_EXTERNAL_STORAGE) == PackageManager.PERMISSION_GRANTED) {
                        Log.i("WONY", "WRITE_EXTERNAL_STORAGE GRANTED");
                        total++;
                    }
                    if (ContextCompat.checkSelfPermission(this, Manifest.permission.ACCESS_NETWORK_STATE) == PackageManager.PERMISSION_GRANTED) {
                        Log.i("WONY", "ACCESS_NETWORK_STATE GRANTED");
                        total++;
                    }
                    if (ContextCompat.checkSelfPermission(this, Manifest.permission.BLUETOOTH_ADMIN) == PackageManager.PERMISSION_GRANTED) {
                        Log.i("WONY", "BLUETOOTH_ADMIN GRANTED");
                        total++;
                    }
                    if (ContextCompat.checkSelfPermission(this, Manifest.permission.BLUETOOTH) == PackageManager.PERMISSION_GRANTED) {
                        Log.i("WONY", "BLUETOOTH GRANTED");
                        total++;
                    }
                    if (ContextCompat.checkSelfPermission(this, Manifest.permission.CALL_PHONE) == PackageManager.PERMISSION_GRANTED) {
                        Log.i("WONY", "CALL_PHONE GRANTED");
                        total++;
                    }
                    Log.i("WONY", "Total Granted Permissions are : "+total);
                    // 권한 허가
                    // 해당 권한을 사용해서 작업을 진행할 수 있습니다
                    if(total == 16) {
                        성공로직
                    }else{
                        //권한 부여 안된 녀석을 알려주고, 다시 앱 실행 여부 묻기..
                        AlertDialog.Builder alertDialogBuilder = new AlertDialog.Builder(PosAuthActivity.this);
                        alertDialogBuilder
                                .setMessage("앱 사용에 필요한 필수 권한이 누락되어 있습니다. 앱을 중지합니다.")
                                .setCancelable(false)
                                .setPositiveButton("확인",
                                        new DialogInterface.OnClickListener() {
                                            public void onClick(
                                                    DialogInterface dialog, int id) {
                                                // 프로그램을 종료한다
                                                PosAuthActivity.this.finish();
                                            }
                                        });
//                                .setNegativeButton("취소",
//                                        new DialogInterface.OnClickListener() {
//                                            public void onClick(
//                                                    DialogInterface dialog, int id) {
//                                                // 다이얼로그를 취소한다
//                                                dialog.cancel();
//                                            }
//                                        });
                        alertDialogBuilder.show();
                    }
                }
                return;
        }
    }
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io