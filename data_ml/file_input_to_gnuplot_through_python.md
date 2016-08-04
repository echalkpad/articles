# File input to gnuplot through python

[Original URL](http://stackoverflow.com/questions/5968721/file-input-to-gnuplot-through-python)

> I am trying to draw a graph of some data I pull out of a MySQL database through python with gnuplot and gnuplot.py. I read the latest 10 lines of data from the MySQL database and store them in a temp...

I am trying to draw a graph of some data I pull out of a MySQL database through python with gnuplot and gnuplot.py. I read the latest 10 lines of data from the MySQL database and store them in a temp file which is supposed to be read by gnuplot. I can do this the manual way by setting through the terminal, but I simply can't see how I should load the file into gnuplot through python. If you can show me an easier way to do this, or simply a way to do it at all, I would be very grateful.

```
import MySQLdb
import Gnuplot

datafile = open('data', 'w+r')

gp = Gnuplot.Gnuplot(persist=1)
gp('set style data lines')
gp('set term png')
gp('set output "escan_graph.png"')
gp('set datafile separator "|"')

db = MySQLdb.connect(host="localhost", user="root", passwd="password", db="zig")
cursor = db.cursor()

cursor.execute("select binaryjs.md cooking_with_docker_and_coreos_on_os_x.md create_a_rest_api_with_nodejs.md data data.json even_easier_restful_api_with_nodejs_and_express_framework.md fix_time_machine_sparsebundle_nas_based_backup_errors.md hacker_codex.md how_can_i_repair_my_hard_drive_if_my_mac_won’t_start.md how_to_create_a_network_backup_with_apples_timemachine.md how_to_create_an_os_x_mavericks_install_drive_updated.md html httpie_parse_url.sh httpsmediumcombibblioorg60youtubechannelsthatwillmakeyousmarter44d8315c2548.md introduction.md just_a_theory.md links_list.txt md null.md parse_urls_json.sh png reclaiming_a_timemachine_volume’s_disk_space.md sqljs.md tidy time_machine_for_every_unix_out_there.md timothycrosleyhug.md url_to_filename.csv from escan_data")

numrows = int(cursor.rowcount) #get the count of total
# get and display one row at a time

if numrows > 10:
 start = numrows-10
else:
 start = 0

for x in range(start,numrows):
 row = cursor.fetchone()
 print row[0], row[1]
 datafile.write(str(row[0]) + "|" + str(row[1]) + "\n")



databuff = Gnuplot.Data(datafile.read(), title="test")


gp.plot(databuff)
```

row[0] is the x-axis and row[1] is y-axis.
