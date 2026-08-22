<summary><b>Low-Level (Column-by-Column) Data Migration Validation</b></summary>

<br>

<table>
  <thead>
    <tr>
      <th>Target Table<br><img width="180" height="1"></th>
      <th>Target Column<br><img width="150" height="1"></th>
      <th>Compare Query (Source vs Target)<br><img width="500" height="1"></th>
      <th>Status<br><img width="80" height="1"></th>
      <th>Defect ID<br><img width="120" height="1"></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td valign='top'><b>AA_ARRANGEMENT_ACTIVITY</b></td>
      <td valign='top'><b>CUSTOMER</b></td>
      <td valign='top'><code>--SDB : ARRANGEMENT ACTIVITY<br>SELECT /*+ PARALLEL(T, 2) PARALLEL(S, 2)*/<br>T.ALTERNATE_ID, T.CUSTOMER, S.CUSTOMER_ID FROM <br>--Target<br>(select ALTERNATE_ID, CUSTOMER<br>from T24PROD.V_FBNK_AA_ARRANGEMENT_ACTIVITY@DBLINK191<br>Where  ALTERNATE_ID IS NOT NULL <br>AND ACTIVITY = 'SAFE.DEPOSIT.BOX-TAKEOVER-ARRANGEMENT'<br>) T<br>inner JOIN <br>--Source<br>(select RECID, CUSTOMER_ID<br>from T24PROD.V_F_EGPL_SAFE_BOXES<br> where FREE_RENTED = 'RENTED'<br>) S<br>ON  T.ALTERNATE_ID = S.RECID <br>where T.CUSTOMER != S.CUSTOMER_ID <br>OR (T.CUSTOMER IS NULL AND S.CUSTOMER_ID IS NOT NULL)  <br>OR (T.CUSTOMER IS NOT NULL AND S.CUSTOMER_ID IS NULL);</code></td>
      <td valign='top'>Pass</td>
      <td valign='top'></td>
    </tr>
    <tr>
      <td valign='top'><b>AA_ARRANGEMENT_ACTIVITY</b></td>
      <td valign='top'><b>CURRENCY</b></td>
      <td valign='top'><code>--SDB : ARRANGEMENT ACTIVITY<br>SELECT  /*+ PARALLEL(T, 4)  PARALLEL(S, 4) */<br>T.ALTERNATE_ID, T.CURRENCY, S.CURRENCY FROM <br>--Target<br>(select ALTERNATE_ID, CURRENCY<br>from T24PROD.V_FBNK_AA_ARRANGEMENT_ACTIVITY@DBLINK191<br>Where  ALTERNATE_ID IS NOT NULL <br>AND ACTIVITY = 'SAFE.DEPOSIT.BOX-TAKEOVER-ARRANGEMENT'<br>) T<br>inner JOIN <br>--Source<br>(select RECID, 'EGP' AS CURRENCY<br>from T24PROD.V_F_EGPL_SAFE_BOXES<br> where FREE_RENTED='RENTED'<br>) S<br>ON  T.ALTERNATE_ID = S.RECID <br>where T.CURRENCY != S.CURRENCY <br>OR (T.CURRENCY IS NULL AND S.CURRENCY IS NOT NULL)  <br>OR (T.CURRENCY IS NOT NULL AND S.CURRENCY IS NULL);</code></td>
      <td valign='top'>Pass</td>
      <td valign='top'></td>
    </tr>
    <tr>
      <td valign='top'><b>AA_ARRANGEMENT_ACTIVITY</b></td>
      <td valign='top'><b>ORIG_CONTRACT_DATE</b></td>
      <td valign='top'><code>--SDB : ARRANGEMENT ACTIVITY<br>SELECT  /*+ PARALLEL(T, 2)  PARALLEL(S, 2) */<br>T.ALTERNATE_ID, T.ORIG_CONTRACT_DATE, S.RENT_START_DATE FROM <br>--Target<br>(select ALTERNATE_ID, ORIG_CONTRACT_DATE<br>from T24PROD.V_FBNK_AA_ARRANGEMENT_ACTIVITY@DBLINK191<br>Where  ALTERNATE_ID IS NOT NULL <br>AND ACTIVITY = 'SAFE.DEPOSIT.BOX-TAKEOVER-ARRANGEMENT'<br>) T<br>inner JOIN <br>--Source<br>(select RECID, RENT_START_DATE <br>from T24PROD.V_F_EGPL_SAFE_BOXES<br> where FREE_RENTED = 'RENTED'<br>) S<br>ON  T.ALTERNATE_ID = S.RECID <br>where T.ORIG_CONTRACT_DATE != S.RENT_START_DATE <br>OR (T.ORIG_CONTRACT_DATE IS NULL AND S.RENT_START_DATE IS NOT NULL)  <br>OR (T.ORIG_CONTRACT_DATE IS NOT NULL AND S.RENT_START_DATE IS NULL);</code></td>
      <td valign='top'>Pass</td>
      <td valign='top'></td>
    </tr>
    <tr>
      <td valign='top'><b>AA_SDB_BOX</b></td>
      <td valign='top'><b>BOX_TYPE</b></td>
      <td valign='top'><code>--SDB : AA.SDB.BOX<br>SELECT  T.ALTERNATE_ID, T.BOX_TYPE, S.SDB_TYPE FROM <br>--Source<br>(select RECID, CASE <br>WHEN SUBSTR(ID, 4,2) IN ('01','04','07','10','13','16') THEN 'SMALL'<br>WHEN SUBSTR(ID, 4,2) IN ('02','05','08','11','14','17') THEN 'MEDIUM'<br>WHEN SUBSTR(ID, 4,2) IN ('03','06','09','12','15','18') THEN 'LARGE'<br>END AS SDB_TYPE <br>from t24prod.v_F_EGPL_SAFE_BOXES<br>) S<br>inner JOIN <br>--Target<br>(select ALTERNATE_ID, BOX_TYPE<br>from t24prod.v_FBNK_AA_SDB_BOX@DBLINK191<br>WHERE ALTERNATE_ID IS NOT NULL) T<br>ON  T.ALTERNATE_ID = S.RECID <br>where T.BOX_TYPE != S.SDB_TYPE <br>OR (T.BOX_TYPE IS NULL AND S.SDB_TYPE IS NOT NULL)  <br>OR (T.BOX_TYPE IS NOT NULL AND S.SDB_TYPE IS NULL);</code></td>
      <td valign='top'>Pass</td>
      <td valign='top'></td>
    </tr>
    <tr>
      <td valign='top'><b>AA_SDB_BOX</b></td>
      <td valign='top'><b>L_SDB_SERIAL_NO</b></td>
      <td valign='top'><code>--SDB : AA.SDB.BOX (L.SDB.SERIAL.NO)<br>SELECT  T.ALTERNATE_ID, T.L_SDB_SERIAL_NO, S.LOCATION FROM <br>--Source<br>(select RECID, LOCATION<br>from t24prod.v_F_EGPL_SAFE_BOXES<br>) S<br>inner JOIN <br>--Target<br>(select ALTERNATE_ID, L_SDB_SERIAL_NO<br>from t24prod.v_FBNK_AA_SDB_BOX@DBLINK191<br>WHERE ALTERNATE_ID IS NOT NULL) T<br>ON  T.ALTERNATE_ID = S.RECID <br>where T.L_SDB_SERIAL_NO != S.LOCATION <br>OR (T.L_SDB_SERIAL_NO IS NULL AND S.LOCATION IS NOT NULL)  <br>OR (T.L_SDB_SERIAL_NO IS NOT NULL AND S.LOCATION IS NULL);</code></td>
      <td valign='top'>Pass</td>
      <td valign='top'><code>CIP2-4201</code><br><code>CIP2-4765</code><br>(Closed)</td>
    </tr>
  </tbody>
</table>
