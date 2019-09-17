For some time now I’ve had this idea rattling around in my head that there 
should be a simple way to export content and metadata from the Content Server 
in a manner that allows for quick access to both the exported content and the 
metadata. Drag & Drop or Export from Webtop only gets you the content files. 
Dump & Load is a major pain, inconsistent, and too opaque. DAR files can also 
be problematic and too opaque.  I wanted a way to quickly export the content 
in its native format and the relevant metadata as XML. These pairs of files 
would then contain everything I needed to know about the content. I could 
visually verify them on the file system; I could open the content with a 
double-click; I could easily examine the metadata or change it; I could 
easily archive it to CD or a ZIP file; or I could import it into another CMS.  
(I think Records Manager does something like this when you disposition files to
NARA as XML.)  Anyway, I finally converted the idea to code. Here is the 
Java/DFC export tool if you are interested.

To run QuikDIE-export, configure the export.properties file to contain the 
necessary login credentials for your repository, and a query to gather the files 
you want to export. In addition, specify a location for the exported content on 
your file system. Here is my sample export.properties file.


export.query=select * from dm_sysobject where folder('/Temp',descend)
export.user=dmadmin
export.password=dmadmin
export.repo=repo1
export.path=c:/Temp/Export
export.log=c:/Temp/Export/export.log

Notes:
- After the export tool runs once it will encrypt the password so it doesn’t sit around unprotected.
- The query must return r_object_id.

Once configured, run the export.bat batch file. The batch file does its best to 
create the correct class path, but you might have to help it if your Documentum 
environment is not installed in the default manner.

The export tool will export all of the objects it gathers from running the 
configured query. Each content object is exported to a file name that is its 
r_object_id (an easy way to avoid any file name collisions), followed by the DOS 
extension for its content type. The accompanying metadata is similarly named, 
followed with .metadata.xml. All files are exported to the path you specify in 
the export.properties file.   Note that no folder structures are created to 
mirror the structure in the repository. That is, the export directory will 
contain a flat file structure (that’s why avoiding file name collisions was 
important). Right now, the export tool can handle dm_sysobjects, dm_folders, 
virtual documents, and content-less objects. Since the export tool will export 
whatever the query returns, you can export versions by specifying ‘(all)‘ in 
the query, and subfolder structures by using the FOLDER(*,'descend') function.

If the export tool encounters an object that appears to be a custom type, it 
will export a .typedef.xml file that contains definitions for all its custom 
attributes.

It’s not perfect, but it works for my purposes.  
