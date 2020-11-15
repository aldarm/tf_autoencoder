<h1>Sparkify Music Song Play Analytics</h1>
<h2>Introduction</h2>
<p>The scope of this database set up is to load songs and user activity from Sparkify's new music streaming app, and be able to analyse what users are listeining to.</p>
<hr size="5" noshade> 
<h2>Data Warehouse setup</h2>
<h3>Fact Table</h3>
<p>The scope of the fact table is to provide metrics and facts on songs being played by users on the Sparkify streaming app. The fact table contains the following columns:</p>
<h4>fact_songplays</h4>
<ul>
    <li>songplay_id</li>
    <li>start_time</li>
    <li>user_id</li>
    <li>level</li>
    <li>song_id</li>
    <li>artist_id</li>
    <li>session_id</li>
    <li>location</li>
    <li>user_agent</li>
</ul>
<p>
<i>songplay_id</i> is the primary key uniquely identifying the fact table row.<br>
<i>start_time</i> is a foreign key referencing the primary key in the dim_time dimension table.<br>
<i>user_id</i> is a foreign key referencing the primary key in the dim_users dimension table.<br>
<i>song_id</i> is a foreign key referencing the primary key in the dim_songs dimension table.<br>
<i>artist_id</i> is a foreign key referencing the primary key in the dim_artists dimension table.<br>   
</p>
<hr>
<h3>Dimension Tables</h3>
<p>The scope of the dimension tables is to provide descriptive attributes to the fact table, when querying the fact table. The dimension tables and their columns are shown below:</p>
<h4>dim_time</h4>
<ul>
    <li>start_time</li>
    <li>hour</li>
    <li>day</li>
    <li>weekofyear</li>
    <li>month</li>
    <li>year</li>
    <li>weekday_num</li>
    <li>weekday_text</li>
</ul>    
<p>
<i>start_time</i> is the primary key uniquely identifying the dimension table row.<br>
This dimension table provides time related attributes when querying the fact table. For example, when joining the fact table with the dim_time dimension table in a SQL query, all fact table rows for a specific month can be queried.<br>
</p>
<h4>dim_users</h4>
<ul>
    <li>user_id</li>
    <li>first_name</li>
    <li>last_name</li>
    <li>gender</li>
    <li>level</li>
</ul>    
<p>
<i>user_id</i> is the primary key uniquely identifying the dimension table row.<br>    
This dimension table provides user related attributes when querying the fact table. For example, when joining the fact table with the dim_users dimension table in a SQL query, all fact table rows for a specific user's name can be queried.<br>    
</p>    
<h4>dim_songs</h4>
<ul>
    <li>song_id</li>
    <li>title</li>
    <li>artist_id</li>
    <li>year</li>
    <li>duration</li>
</ul>    
<p>
<i>song_id</i> is the primary key uniquely identifying the dimension table row.<br>
<i>artist_id</i> is a foreign key referencing the primary key in the dim_artists dimension table.<br>    
This dimension table provides song related attributes when querying the fact table. For example, when joining the fact table with the dim_songs dimension table in a SQL query, all fact table rows related to a specific song title can be queried.<br>    
</p>    
<h4>dim_artists</h4>
<ul>
    <li>artist_id</li>
    <li>artist</li>
    <li>location</li>
    <li>latitude</li>
    <li>longitude</li>        
</ul>    
<p>
<i>artist_id</i> is the primary key uniquely identifying the dimension table row.<br>    
This dimension table provides artist related attributes when querying the fact table. For example, when joining the fact table with the dim_artists dimension table in a SQL query, all fact table rows related to a specific artist can be queried.<br>    
</p>
<hr>
<h3>Schema</h3>
<p>The fact table references the dimension tables using a star schema, as shown in the figure below.</p>
<img src="./star_schema.png" alt="Star Schema" style="width:612px;height:378px;">
<hr size="5" noshade> 
<h2>ETL pipeline</h2>
<h3>First time Database setup</h3>
<p>To create the database and and set up the data warehouse fact and dimension tables, a one-time python script needs to be executed. The command to be executed, if executed from within Jupyter Notebook, is the one shown below:</p>
<p>  
%run -i './create_tables.py'    
</p>
<hr>
<h3>Loading data</h3>
<p>When the database and tables have been created, a python script needs to be executed to populate the fact and dimension tables. Tables are populated with data from song and log files. The command to be executed, if executed from within Jupyter Notebook, is the one shown below:</p>
<p>
%run -i './etl.py'
</p>
<p>This ETL script has some built-in error handling. If one of the files being loaded contains invalid data, instead of causing the ETL to fail and stop, data from that file is not loaded and the ETL can continue with the rest of the files. An example of this can be seen from the figure below. The file <i>TRAAAAW128F429D538e.json</i> contains a non-numeric character in the duration field and would thus cause the ETL to fail if not handled.</p>
<img src="./file_error.png" alt="Error handling" style="width:636px;height:268px;">    
<hr size="15" noshade> 
<h2>Analytic queries</h2>
<p>When data has been loaded to the data warehouse, a number of reports are available giving insight into the data. To access these reports the command to be executed, if executed from within Jupyter Notebook, is the one shown below:</p>
<p>
%run -i './etl_reports.py'
</p>
<p>A menu will be displayed, and the desired report number can be entered.</p>
<img src="./reports.png" alt="Analytic reports" style="width:1083px;height:370px;">   
<hr>
<h3>Report description</h3>
<h4>Top 5 users' songplays count by level</h4>
<p>This report lists the top 5 users using the free service, and the top 5 users using the paid service.</p>
<img src="./report1.png" alt="Top 5 users' songplays count by level">   
<hr>
<h4>Songplays count by day of month, hour, level</h4>
<p>This report asks for the desired year, month and day to display, then lists the free and paid song plays count by hour for the day which has been entered. The lowest values are highlighted in red, whilst the highest values are highlighted in green.</p>
<img src="./report2.png" alt="Top 5 users' songplays count by level"> 
<hr>
<h4>Songplays count by day of month, level</h4>
<p>This report lists the free and paid song plays count by day of month. The lowest values are highlighted in red, whilst the highest values are highlighted in green.</p>
<img src="./report3.png" alt="Top 5 users' songplays count by level"> 
<hr>
<h4>Songplays count by hour, level</h4>
<p>This report lists the free and paid song plays count by hour. The lowest values are highlighted in red, whilst the highest values are highlighted in green.</p>
<img src="./report4.png" alt="Top 5 users' songplays count by level"> 
<hr>
<h4>Songplays count by weekday, level</h4>
<p>This report lists the free and paid song plays count by day of the week. The lowest values are highlighted in red, whilst the highest values are highlighted in green.</p>
<img src="./report5.png" alt="Top 5 users' songplays count by level"> 
<hr>
<h4>Songplays count of users with free and paid songplays</h4></b></p>
<p>This report lists the users using both the free service and the paid service. The lowest values are highlighted in red, whilst the highest values are highlighted in green.</p>
<img src="./report6.png" alt="Top 5 users' songplays count by level"> 