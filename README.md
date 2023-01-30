# tcpdf-with-my-sql-data-border-not-work-in-all-row
tcpdf with my sql data border not work in all row
include_once('tcpdf_6_2_13/tcpdf/tcpdf.php');
///$inv_mst_query = "SELECT T1.MST_ID, T1.INV_NO, T1.CUSTOMER_NAME, T1.CUSTOMER_MOBILENO, T1.ADDRESS FROM INVOICE_MST T1 WHERE T1.MST_ID='".$MST_ID."' ";    
$sql ="select * from accuracy_report where date=CURRENT_DATE() && paraname like '%SSC-CHSL%' ORDER BY paragraph_id DESC,total_type_word DESC,incorrect_word asc";
$results = mysqli_query($conn,$sql); 
$count = 0;

$name="             Rank        Candidate Name      User Address     Test Detils    Total Character Total Error     Student-Gross   Student-Net       Result";

$pdf = new TCPDF('L', PDF_UNIT, PDF_PAGE_FORMAT, true, 'UTF-8', false);
$pdf->SetCreator(PDF_CREATOR);  
$pdf->SetTitle("Export HTML Table data to PDF using TCPDF in PHP");  
$pdf->SetHeaderData('', '', $name);  
$pdf->setHeaderFont(Array(PDF_FONT_NAME_MAIN, '', PDF_FONT_SIZE_MAIN));  
$pdf->setFooterFont(Array(PDF_FONT_NAME_DATA, '', PDF_FONT_SIZE_DATA));  
$pdf->SetDefaultMonospacedFont('helvetica');  
$pdf->SetFooterMargin(PDF_MARGIN_FOOTER);  
$pdf->SetMargins(PDF_MARGIN_LEFT, '10', PDF_MARGIN_RIGHT);  
$pdf->setPrintHeader(true);  
$pdf->setPrintFooter(false);  
$pdf->SetAutoPageBreak(TRUE, 10);  
$pdf->SetFont('helvetica', '', 12);
$pdf->SetFillColor(0, 128, 128); // sets the fill color of the cells
$pdf->SetDrawColor(0, 0, 0); // sets the border color of the cells
$pdf->SetLineWidth(0.3); // sets the width of the border
$pdf->AddPage();


$content = '';
$content .= '
  <table cellpadding="0" cellspacing="0" style="width:100%;">
    <table class="table table-striped">
    
      <tbody>';

while ($rows = $results->fetch_assoc()) {
  $count++;
  $id = $rows['user_id'];
  $inv_mst_query = "SELECT user_name, user_address from user_regestration where id ='$id' ";
  $inv_mst_results = mysqli_query($conn, $inv_mst_query);   
  $inv_mst_data_row = mysqli_fetch_array($inv_mst_results, MYSQLI_ASSOC);
  $user = $inv_mst_data_rowra['user_name'];
  $address =  $inv_mst_data_row['user_address'];
  $stuexam = $inv_mst_data_row['EXAM'];
        $netspeed=$rows['sgross_speed']-($rows['incorrect_word']/10);
                    $ntpc=$rows['ntpc'];
                    $stuexam= $inv_mst_data_row['EXAM'];
                    $stugross=$rows['total_type_word']/5/$rows['stutime'];
                    $granderror=$rows['incorrect_word']+$rows['typeextra'];
                    $stunet=$stugross-($granderror/$rows['stutime']);
                    $sgrosskeystroke=$rows['typed_character']/5/$rows['stutime'];
                    $snetkeystroke=$sgrosskeystroke-($granderror/$rows['stutime']);
                    $remaks;
                     if ($rows['total_type_word'] >= '1630' && $rows['incorrect_word'] <=24) {
                         $remarks = "CHSL-Pass";} 
  
  if ($rows['incorrect_word'] <= 10) {
    $pdf->SetFillColor(0, 128, 128); // sets the fill color of the cells
    $pdf->SetDrawColor(0, 0, 0); // sets the border color of the cells
  } else {
    $pdf->SetFillColor(255, 0, 0); // sets the fill color of the cells
    $pdf->SetDrawColor(0, 0, 0); // sets the border color of the cells
  }
  
  $content .= '
     <tr>
  <td>'.$count.'</td>
  <td>'.$inv_mst_data_row['user_name'].'</td>
  <td>'.$inv_mst_data_row['user_address'].'</td>
  <td>'.$rows['paraname'].'</td>
  <td>'.$rows['total_type_word'].'</td>
  <td>'.$rows['incorrect_word'].'</td>
  <td>'.round($stugross,2).'</td>
  <td>'.round($stunet,2).'</td>
  <td>'.$remarks.'</td>
</tr>
  
    </table>
  </table>';
}

$totalPages = $pdf->getNumPages();
$page = (isset($_GET['page']) && is_numeric($_GET['page'])) ? $_GET['page'] : 1;

if ($page > 0 && $page <= $totalPages) {
    $pdf->setPage($page);
} else {
    die("Error: Invalid page number");
}

$pdf->writeHTML($content);


$file_location = "/home/fbi1glfa0j7p/public_html/examples/generate_pdf/uploads/"; //add your full path of your server
//$file_location = "/opt/lampp/htdocs/examples/generate_pdf/uploads/"; //for local xampp server

$datetime=date('dmY_hms');
$file_name = "CHSL22_".$datetime.".pdf";
ob_end_clean();

if($_GET['ACTION']=='VIEW') 
{
    
	$pdf->Output($file_name, 'I'); // I means Inline view
} 
else if($_GET['ACTION']=='DOWNLOAD')
{
	$pdf->Output($file_name, 'D'); // D means download
}
else if($_GET['ACTION']=='UPLOAD')
{
$pdf->Output($file_location.$file_name, 'F'); // F means upload PDF file on some folder
echo "Upload successfully!!";
}

//----- End Code for generate pdf


?>
