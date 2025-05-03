# fingerprint_Atm
 MAIN.PY

from flask import Flask, render_template, request, redirect, url_for, session import pymysql
from datetime import datetimeapp = Flask(  name  )

app.secret_key = 'welcome' global uname
global uname

@app.route('/Deposit', methods=['GET', 'POST']) def Deposit():
output = '<tr><td><font size="3" color="black">Username</td><td><input type="text" name="t1" size="20" value='+uname+' readonly/></td></tr>'
return render_template('Deposit.html', msg1=output) @app.route('/Withdraw', methods=['GET', 'POST']) def Withdraw():
output = '<tr><td><font size="3" color="black">Username</td><td><input type="text" name="t1" size="20" value='+uname+' readonly/></td></tr>'
return render_template('Withdraw.html', msg1=output) @app.route('/index', methods=['GET', 'POST'])
 
 
def index():

return render_template('index.html', msg='') @app.route('/Login', methods=['GET', 'POST']) def Login():
return render_template('Login.html', msg='') @app.route('/Signup', methods=['GET', 'POST']) def Signup():
return render_template('Signup.html', msg='') @app.route('/ViewBalance', methods=['GET', 'POST']) def ViewBalance():
font = "<font size='3' color='black'>" output = ""
con = pymysql.connect(host='127.0.0.1',port = 3308,user = 'root', password = 'root', database = 'atm',charset='utf8')
with con:

cur = con.cursor()

cur.execute("select * FROM transaction where username='"+uname+"'") rows = cur.fetchall()
for row in rows:
 
 
output+="<tr><td>"+font+str(row[0])+"</font></td>" output+="<td>"+font+str(row[1])+"</font></td>" output+="<td>"+font+str(row[2])+"</font></td>" output+="<td>"+font+str(row[3])+"</font></td>" output+="<td>"+font+str(row[4])+"</font></td>"
return render_template('ViewBalance.html', msg=output) @app.route('/LoginAction', methods=['GET', 'POST'])
def LoginAction(): global uname
if request.method == 'POST': user = request.form['t1'] password = request.form['t2'] data = request.files['t3'].read() index = 0
con = pymysql.connect(host='127.0.0.1',port = 3308,user = 'root', password = 'root', database = 'atm',charset='utf8')
with con:
cur = con.cursor() cur.execute("select * FROM users")
 
 
rows = cur.fetchall() for row in rows:
if row[0] == user and password == row[1]:

in_file = open("E:/2021/MAJOR PROJECTS/PROJECT CODES/36.Fingerprint based on ATM System/FingerprintATM/static/users/"+user+".png", "rb")
avail_data = in_file.read() # if you only wanted to read 512 bytes, do
.read(512)

in_file.close()

if avail_data == data: index = 1
uname = user break
if index == 0:

return render_template('Login.html', msg="Invalid login details") else:
return render_template('UserScreen.html', msg="Welcome "+uname) def getAmount(user):
amount = 0
 
 
con = pymysql.connect(host='127.0.0.1',port = 3308,user = 'root', password = 'root', database = 'atm',charset='utf8')
with con:

cur = con.cursor()

cur.execute("select * FROM transaction") rows = cur.fetchall()
for row in rows:

if row[0] == user: amount = float(row[4]) break
return amount

@app.route('/WithdrawAction', methods=['GET', 'POST']) def WithdrawAction():
if request.method == 'POST': user = request.form['t1'] amount = request.form['t2'] total = getAmount(user) withdraw = float(amount)
status = "Error in depositing amount"
 
 
if total > withdraw:

total = total - withdraw
timestamp = datetime.now().strftime('%Y-%m-%d %H:%M:%S') student_sql_query = "update transaction set

transaction_amount='"+amount+"',transaction_type='Withdrawl',transaction_date='"+ str(timestamp)+"',total_balance='"+str(total)+"' where username='"+user+"'"
db_connection = pymysql.connect(host='127.0.0.1',port = 3308,user = 'root', password = 'root', database = 'atm',charset='utf8')
db_cursor = db_connection.cursor() db_cursor.execute(student_sql_query) db_connection.commit()
if db_cursor.rowcount == 1:

status = "Withdrawl Transaction Successfull"

else:

status = "Insufficient Fund"

return render_template('UserScreen.html', msg=status) @app.route('/DepositAction', methods=['GET', 'POST']) def DepositAction():
if request.method == 'POST':
 
 
user = request.form['t1'] amount = request.form['t2'] total = getAmount(user)
status = "Error in depositing amount" if total == 0:
total = total + float(amount)
timestamp = datetime.now().strftime('%Y-%m-%d %H:%M:%S') student_sql_query = "INSERT INTO

transaction(username,transaction_amount,transaction_type,transaction_date,total_bala nce)  VALUES('"+user+"','"+amount+"','Deposit','"+str(timestamp)+"','"+str(total)+"')"
db_connection = pymysql.connect(host='127.0.0.1',port = 3308,user = 'root', password = 'root', database = 'atm',charset='utf8')
db_cursor = db_connection.cursor() db_cursor.execute(student_sql_query) db_connection.commit()
if db_cursor.rowcount == 1:

status = "Transaction Successfull" elif total > 0:
 
 
total = total + float(amount)
timestamp = datetime.now().strftime('%Y-%m-%d %H:%M:%S') student_sql_query = "update transaction set
transaction_amount='"+amount+"',transaction_type='Deposit',transaction_date='"+str( timestamp)+"',total_balance='"+str(total)+"' where username='"+user+"'"
db_connection = pymysql.connect(host='127.0.0.1',port = 3308,user = 'root', password = 'root', database = 'atm',charset='utf8')
db_cursor = db_connection.cursor() db_cursor.execute(student_sql_query) db_connection.commit()
if db_cursor.rowcount == 1:

status = "Transaction Successfull"

return render_template('UserScreen.html', msg=status) @app.route('/SignupAction', methods=['GET', 'POST'])
def SignupAction():

if request.method == 'POST': user = request.form['t1'] password = request.form['t2'] phone = request.form['t3']
 
 
email = request.form['t4'] address = request.form['t5'] gender = request.form['t6'] data = request.files['t7'].read() status = "none"
con = pymysql.connect(host='127.0.0.1',port = 3308,user = 'root', password = 'root', database = 'atm',charset='utf8')
with con:

cur = con.cursor() cur.execute("select * FROM users") rows = cur.fetchall()
for row in rows:

if row[0] == user:

status = user+" Username already exists" break
if status == 'none':

db_connection = pymysql.connect(host='127.0.0.1',port = 3308,user = 'root', password = 'root', database = 'atm',charset='utf8')
db_cursor = db_connection.cursor()
 
 
student_sql_query = "INSERT INTO users(username,password,contact_no,emailid,address,gender) VALUES('"+user+"','"+password+"','"+phone+"','"+email+"','"+address+"','"+gender+ "')"
db_cursor.execute(student_sql_query) db_connection.commit()
if db_cursor.rowcount == 1:

out_file = open("E:/2021/MAJOR PROJECTS/PROJECT CODES/36.Fingerprint based on ATM System/FingerprintATM/static/users/"+user+".png", "wb")
out_file.write(data) out_file.close()
status = 'Signup process completed'

return render_template('Signup.html', msg=status)




@app.route('/Logout') def Logout():
return render_template('index.html', msg='') if  name	== ' main ':
app.run()
 
 
Login.html

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">

<head>

<meta http-equiv="content-type" content="text/html; charset=utf-8" />

<title>Fingerprint based on ATM System</title>

<meta name="keywords" content="" />

<meta name="Premium Series" content="" />

<link rel="stylesheet" href="{{ url_for('static', filename='default.css') }}">

<script language="javascript"> function validate(formObj)
{

if(formObj.t1.value.length==0)

{

alert("Please Enter username"); formObj.t1.focus();
return false;

}
 
 
if(formObj.t2.value.length==0)

{

alert("Please Enter password"); formObj.t2.focus();
return false;

}

if(formObj.t3.value.length==0){ alert("Please upload finger print image"); formObj.t3.focus();
return false;

}

formObj.actionUpdateData.value="update"; return true;
}

</script>

</head>

<body>

<!-- start header -->
 
 
<div id="header">

<div id="logo">

<h1><span><center>Fingerprint based on ATM System

</center></span></h1>




</div>

<div id="menu">

<ul id="main">

<li class="current_page_item"><a href="index">Home</a></li><td></td>
<li><a href="Login">Login Here</a></li><td></td>

<li><a href="Signup">Signup Here</a></li><td></td>

<td></td>




</ul>




</div>




</div>
 
 
<div id="wrapper">

<!-- start page -->

<div id="page">

<form name ="frm" method="post" action="/LoginAction" enctype="multipart/form-data" onsubmit="return validate(this);">
<!-- start content -->

<div id="content">

<div class="flower"><center><h1>



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nb sp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;& nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
User Login Screen<h1></div>

<br/>

<div class="msg"><font size="3" color="red"><center>{{ msg

}}</center></font></div>

<br/>







<table align="right">
 
 
<tr><td><font size="3" color="black">Username</td><td><input type="text" name="t1" size="20"/></td></tr>
<tr><td><font size="3" color="black">Password</td><td><input type="password" name="t2" size="20"/> </td></tr>
<tr><td><font size="3" color="black">Upload&nbsp;Fingerprint</td><td><input type="file" name="t3" size="35"/></td></tr>
<tr><td></td><td><input type="submit" value="Login"></td>

</td></tr></table>

</form>

</table>

</center><br /><div class="post">




<div class="entry">




</div>

</div>

</div>

<!-- end content -->

<!-- start sidebars -->
 
 
<!-- end sidebars -->

<div style="clear: both;">&nbsp;</div>

</div>

<!-- end page -->

</div>

</body>

</html>

Deposit.html

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">

<head>

<meta http-equiv="content-type" content="text/html; charset=utf-8" />

<title>Fingerprint based on ATM System</title>

<meta name="keywords" content="" />

<meta name="Premium Series" content="" />

<link rel="stylesheet" href="{{ url_for('static', filename='default.css') }}">
<script language="javascript"> function validate(formObj) {
 
 
if(formObj.t2.value.length==0)

{

alert("Please Enter amount"); formObj.t2.focus();
return false;

}

return true;

}

</script>

</head>

<body>

<!-- start header -->

<div id="header">

<div id="logo">

<h1><span><center>Fingerprint based on ATM System

</center></span></h1>




</div>
 
 
<div id="menu">

<ul id="main">

<li class="current_page_item"><a href="Deposit">Deposit Amount</a></li><td></td>
<li><a href="Withdraw">Withdrawl Amount</a></li><td></td>
<li><a href="ViewBalance">View Balance</a></li><td></td>

<li><a href="Logout">Logout</a></li><td></td>

<td></td>




</ul>




</div>




</div>

<div id="wrapper">

<!-- start page -->

<div id="page">
 
 
<form name ="frm" method="post" action="/DepositAction" onsubmit="return validate(this);">
<!-- start content -->

<div id="content">

<div class="flower"><center><h1>



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nb sp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;& nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
Amount Deposit Screen<h1></div>

<br/>

<div class="msg"><font size="3" color="red"><center>{{ msg
}}</center></font></div>

<br/>







<table align="right">

{{ msg1 | safe }}

<tr><td><font size="3" color="black">Amount</td><td><input type="text" name="t2" size="20"/> </td></tr>
 
 
<tr><td></td><td><input type="submit" value="Submit"></td>

</td></tr></table>

</form>

</table>

</center><br /><div class="post">




<div class="entry">




</div>

</div>

</div>

<!-- end content -->

<!-- start sidebars -->

<!-- end sidebars -->

<div style="clear: both;">&nbsp;</div>

</div>

<!-- end page -->

</div>
 
 
</body>

</html>
