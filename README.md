# sap-test-api
Testing the functionality of the SWAPI

* STEP 1: Define Variables
```
  DATA: lv_code          TYPE i,
        lv_url           TYPE string,
        li_client        TYPE REF TO if_http_client,
        lt_errors        TYPE TABLE OF string,
        lv_error_message TYPE string,
        lv_json_data     TYPE string.
```

* STEP 2: Set the API Endpoint
```
  lv_url = 'https://swapi.dev/api/starships'.
```

* STEP 3: Create an HTTP Client
```
  cl_http_client=>create_by_url(
  EXPORTING
    url     = lv_url
  IMPORTING
    client  = li_client ).
```

* STEP 4: Send the HTTP Request
```
  li_client->send( ).
```

* STEP 5: Receive the HTTP Response
```
  li_client->receive(
    EXCEPTIONS
      http_communication_failure  = 1
      http_invalid_state          = 2
      http_processing_failed      = 3
      OTHERS                      = 4 ).
```

* STEP 6: Handle Errors
```
  IF sy-subrc <> 0.
  WRITE: / 'Error Number', sy-subrc, /.
  MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
  li_client->get_last_error(
    IMPORTING
      message = lv_error_message ).
  SPLIT lv_error_message AT cl_abap_char_utilities=>newline INTO TABLE lt_errors.
  LOOP AT lt_errors INTO lv_error_message.
    WRITE: / lv_error_message.
  ENDLOOP.
  RETURN.
ENDIF.
```

* STEP 7: Check the HTTP Status Code
```
  li_client->response->get_status(
    IMPORTING
      code = lv_code ).
```

* STEP 8: Process the Response
```
  IF lv_code = 200.
    WRITE: / lv_url, ': OK', icon_checked AS ICON.
    WRITE: /.
  
    lv_json_data = li_client->response->get_cdata( ).
    WRITE lv_json_data.
  ENDIF.
```

Make sure that you have install the SSL Certificate from the SWAPI and add to STRUTS t-code.



