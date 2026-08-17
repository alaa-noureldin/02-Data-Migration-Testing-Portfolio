Scroll Right and Left to see the whole query structure.

<details>
<summary><b>01-Safe Deposit Box (SDB) High-Level Data Migration Queries</b></summary>

<br>

<table>
  <thead>
    <tr>
      <!-- Adjust these widths to shrink or expand the columns -->
      <th>Description<br><img width="150" height="1"></th>
      <th>Source Query (High Level Analysis)<br><img width="350" height="1"></th>
      <th>Target Query (High Level Analysis)<br><img width="350" height="1"></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td valign='top'><b>Getting count per SDB box type</b></td>
      <td valign='top'><code>select CASE <br> WHEN SUBSTR(ID, 4,2) IN ('01','04','07','10','13','16') THEN 'SMALL' <br>WHEN SUBSTR(ID, 4,2) IN ('02','05','08','11','14','17') THEN 'MEDIUM' <br>WHEN SUBSTR(ID, 4,2) IN ('03','06','09','12','15','18') THEN 'LARGE'<br>else 'New Products Found not identified'<br> END AS SDB_TYPE, count(ID) AS SourceRecordCount <br>from t24prod.v_F_EGPL_SAFE_BOXES<br> group by ( CASE  <br> WHEN SUBSTR(ID, 4,2) IN ('01','04','07','10','13','16') THEN 'SMALL' <br>WHEN SUBSTR(ID, 4,2) IN ('02','05','08','11','14','17') THEN 'MEDIUM' <br>WHEN SUBSTR(ID, 4,2) IN ('03','06','09','12','15','18') THEN 'LARGE'<br>else 'New Products Found not identified' END);</code></td>
      <td valign='top'><code>select BOX_TYPE, count(RECID) AS TARGETRecordCOUNT <br> from t24prod.v_FBNK_AA_SDB_BOX <br> group by (BOX_TYPE);</code></td>
    </tr>
    <tr>
      <td valign='top'><b>Getting count per status</b></td>
      <td valign='top'><code>select distinct CASE WHEN FREE_RENTED = 'FREE' THEN 'AVAILABLE'<br>ELSE FREE_RENTED END AS STATUS, count(*) as SourceRecordCount <br>from t24prod.v_F_EGPL_SAFE_BOXES<br>group by ( CASE WHEN FREE_RENTED = 'FREE' THEN 'AVAILABLE'<br>ELSE FREE_RENTED END )<br>;</code></td>
      <td valign='top'><code>select distinct  T1.STATUS as status_SDB_BOX, count(T1.RECID) AS TARGETRecordCOUNT<br>from t24prod.v_FBNK_AA_SDB_BOX T1<br>group by ( T1.STATUS )<br>order by T1.STATUS DESC;</code></td>
    </tr>
    <tr>
      <td valign='top'><b>Getting count per status and SDB type</b></td>
      <td valign='top'><code>select distinct  CASE WHEN FREE_RENTED = 'FREE' THEN 'AVAILABLE'<br>ELSE FREE_RENTED END AS STATUS, CASE  <br>WHEN SUBSTR(ID, 4,2) IN ('01','04','07','10','13','16') THEN 'SMALL' <br>WHEN SUBSTR(ID, 4,2) IN ('02','05','08','11','14','17') THEN 'MEDIUM' <br>WHEN SUBSTR(ID, 4,2) IN ('03','06','09','12','15','18') THEN 'LARGE' <br>else  'New Products Found not identified'<br>END AS SDB_TYPE, count(*) as SourceRecordCount<br>from t24prod.v_F_EGPL_SAFE_BOXES<br>group by ( CASE WHEN FREE_RENTED = 'FREE' THEN 'AVAILABLE'<br>ELSE FREE_RENTED END, CASE  <br>WHEN SUBSTR(ID, 4,2) IN ('01','04','07','10','13','16') THEN 'SMALL' <br>WHEN SUBSTR(ID, 4,2) IN ('02','05','08','11','14','17') THEN 'MEDIUM' <br>WHEN SUBSTR(ID, 4,2) IN ('03','06','09','12','15','18') THEN 'LARGE' <br>else  'New Products Found not identified'<br>END)<br>order by SDB_TYPE DESC;</code></td>
      <td valign='top'><code>--Get count of all SDB types with status: Available & rented<br>select distinct T1.STATUS as status_SDB_BOX,T1.BOX_TYPE, count(T1.RECID) AS TARGETRecordCOUNT<br>from t24prod.v_FBNK_AA_SDB_BOX T1<br>group by ( T1.STATUS, T1.BOX_TYPE )<br>order by T1.BOX_TYPE DESC;<br><br>-- Get count of only Rented SDB type<br>select count(ALTERNATE_ID) as TARGETRecordCOUNT<br>  from T24PROD.V_FBNK_AA_ARRANGEMENT_ACTIVITY <br> where ACTIVITY='SAFE.DEPOSIT.BOX-TAKEOVER-ARRANGEMENT';</code></td>
    </tr>
    <tr>
      <td valign='top'><b>Getting count per branch, status and SDB type</b></td>
      <td valign='top'><code>select distinct   CASE  <br>WHEN SUBSTR(ID, 4,2) IN ('01','04','07','10','13','16') THEN 'SMALL' <br>WHEN SUBSTR(ID, 4,2) IN ('02','05','08','11','14','17') THEN 'MEDIUM' <br>WHEN SUBSTR(ID, 4,2) IN ('03','06','09','12','15','18') THEN 'LARGE'<br>else  'New Products Found not identified'<br>END AS SDB_TYPE , CASE WHEN FREE_RENTED = 'FREE' THEN 'AVAILABLE'<br>ELSE FREE_RENTED END AS STATUS, ID_BRANCH,  count(*) as SourceRecordCount<br>from t24prod.v_F_EGPL_SAFE_BOXES<br>group by (ID_BRANCH, FREE_RENTED, CASE  <br>WHEN SUBSTR(ID, 4,2) IN ('01','04','07','10','13','16') THEN 'SMALL' <br>WHEN SUBSTR(ID, 4,2) IN ('02','05','08','11','14','17') THEN 'MEDIUM' <br>WHEN SUBSTR(ID, 4,2) IN ('03','06','09','12','15','18') THEN 'LARGE'<br>else  'New Products Found not identified'<br>END)<br>order by SDB_TYPE DESC;</code></td>
      <td valign='top'><code>select distinct T1.BOX_TYPE ,  T1.STATUS as status_SDB_BOX, SUBSTR(T1.ALTERNATE_ID, 1,2) AS branch, count(T1.RECID) AS TARGETRecordCOUNT<br>from t24prod.v_FBNK_AA_SDB_BOX T1<br>group by (SUBSTR(T1.ALTERNATE_ID, 1,2), T1.STATUS, T1.BOX_TYPE )<br>order by T1.BOX_TYPE DESC;</code></td>
    </tr>
  </tbody>
</table>

</details>
