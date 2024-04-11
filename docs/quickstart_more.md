# Additional settings
Exment can be executed by performing the [installation procedure](/quickstart.md).  
However, additional settings may be required to use some functions.  
When using the following functions, set according to the procedure.  

### List of settings

- [Change / delete "admin" in URL Change / delete](/additional_prefix)  
"/admin" in URL, which is normally included.

- [Task Schedule](/additional_task_schedule)  
This is a setting method for performing automatic processing at a certain time, such as automatic creation of backups and notification of deadlines.  

- [Server external communication off](/additional_disable_outside_api)  
This is the setting method to stop communication from the server to the external server, such as information on Exment updates.

- [Enable Caching Cache](/additional_cache)  
Master data such as custom tables, columns, views, and workflows.  
Depending on the environment, it may be faster.

- [Change File Save Location](/additional_file_saveplace)  
This is the procedure to change the file save destination from local server to FTP or AWS S3.

- [Change file upload upper limit size / memory size](/additional_php_ini)  
This is the procedure to change the upload size of attached file and PHP memory usage.

- [IP filter](/additional_ip_filter)  
Web page requests and API execution can be restricted by IP address.

- [When 429 error occurs in API](additional_429_too_many)  
This is the solution when 429 Too Many Requests occurs during API implementation.

- [Add label column with data acquisition API](/additional_api_label)  
You can add the "label" column when you get the data list or create / update new data.

- [Add original option in the setting "Available characters" of column type "1 line text"](/additional_available_characters)  
Custom column type "One line text" setting You can add available options in "Available characters".

- [Delayed execution of notification processing](/additional_queue)  
Some processes such as notifications can be delayed (queued).

- [Reverse proxy settings](/additional_reverse_proxy)  
This is an additional setting when building an environment with a reverse proxy.

- [Expert mode](/additional_expert)  
This is a procedure for displaying more flexible setting items.