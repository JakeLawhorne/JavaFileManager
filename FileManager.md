# FileManager

An amazingly useless tool!

## Console.java
```
public String promptForString(String prompt) {
        System.out.println(prompt);
        String inputString = s.nextLine().toLowerCase().trim();
        return inputString;
    }
```
This method is the user prompt return in the terminal.

```
public void sendMessage(String message) {
        System.out.println(message);
    }
}
```

This method prints a message to the terminal.

## FileOperator.java

```
public void list(String path){
        File path1 = new File(path);
        if(path1.exists() && path1.isDirectory()){
            String[] lista = path1.list();
            if(lista.length == 0){
                cons.sendMessage("Folder is empty.");
            } else {
                for (String s : lista) {
                    cons.sendMessage(s);
                }
            }
        } else {
            cons.sendMessage("Wrong path entered!");
        }
    }
```
This method checks to see if the path entered exists, then assigns a String[] with the values of that list.
If the list is empty, it will print "Folder is empty".
Else, it will list the contents of that directory.
If command is entered incorrectly, the terminal will print "Wrong path entered."

```
 public void info(String path){
        File path1 = new File(path);
        if(path1.exists()){
            cons.sendMessage("Name: " + path1.getName());
            cons.sendMessage("Absolute path: " + path1.getAbsolutePath());
            cons.sendMessage("Relative path: " + path1.getPath());
            cons.sendMessage("Size: " + path1.length());
            Path p = Paths.get(path);
            try {
                BasicFileAttributes bfa = Files.readAttributes(p, BasicFileAttributes.class);
                cons.sendMessage("Created: " + time(bfa.creationTime().toMillis()));
            } catch (IOException ex) {
                cons.sendMessage(ex.toString());
            }
            cons.sendMessage("Last Modified: " + time(path1.lastModified()));
        } else {
            cons.sendMessage("Wrong path entered!");
        }
    }
```
The info method prints all the attributes of a file if the path exists.
This includes:
1. File Name
2. AbsolutePath
3. RelativePath
4. Size of file

Next it will attempt to read the files attributes and print the time it was created.
If not possible, it will print an IOException.
Then it prints the last time it was modified.
Finally, it will print that the wrong path was entered if inputed incorrectly;

```
public String time(long l){
        Instant instant = Instant.ofEpochMilli(l);
        LocalDateTime dateTime = LocalDateTime.ofInstant(instant, ZoneId.systemDefault());
        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("dd. MMMM yyyy. HH:mm:ss");
        return dateTime.format(dateTimeFormatter);
    }
```
This method takes a  Long parameter and returns it as a date/time.

```
public void createDir(String path){
        File folder = new File(path);
        String s = folder.getName();
        boolean illegalChar = checkForIllegalChars(s);
        try {
            if(!folder.exists()){
                if(illegalChar){
                    cons.sendMessage("A file name can't contain any of the following characters: ");
                    cons.sendMessage(illegalcharsstring);
                } else {
                    makeDir(folder.getParentFile().exists(), folder);
                    cons.sendMessage("Created a folder called " + folder.getName());
                }
            } else {
                cons.sendMessage("Folder called " + folder.getName() + " already exists.");
            }
        } catch (Exception e) {
            cons.sendMessage("Couldn't create a folder called " + folder.getName());
        }
    }
```
This method creates a new File called folder.
If the input contains any illegal characters it will re-prompt you.
If the file doesn't exist, it will create that file.
If the file DOES exist it will tell you it already exists.
Then if it throws an exception it tells you the file failed to generate.

```
 private boolean checkForIllegalChars(String s) {
        for (char kh : illegalchars) {
            if (s.indexOf(kh) >= 0) {
                return true;
            }
        }
        return false;
    }
```
This method just checks if any illegal characters were entered.
```
public void rename(String of, String nf) {
        File oldFile = new File(of);
        String[] strings = of.split("\\\\+");
        int n = strings.length;
        strings[n-1] = nf;
        StringBuilder sb = new StringBuilder();
        sb.append(strings[0]);
        sb.append("\\");
        for(int i = 1; i <strings.length; i++){
            sb.append("\\");
            sb.append(strings[i]);
        }
        File newFile = new File(sb.toString());
        if(newFile.exists()){
            cons.sendMessage("File with desired name already exists!");
            return;
        }
        if(oldFile.renameTo(newFile)){
            cons.sendMessage("Rename succesful.");
        } else {
            cons.sendMessage("Rename failed!");
        }
    }
```
This method attempts to change the name of an existing file.
If a file with that name already exists, or it throws an IOException it fails.
Else it will rename the file.

```
public void copyCut(File f1, File f2, String c){
        try (FileInputStream inStream = new FileInputStream(f1);
             FileOutputStream outStream = new FileOutputStream(f1);) {
            byte[] buffer = new byte[1024];
            int length;
            while ((length = inStream.read(buffer)) > 0) {
                outStream.write(buffer, 0, length);
            }
            inStream.close();
            outStream.close();
            if("move".equals(c)){
                f1.delete();
                cons.sendMessage("Moving is successfuly finished.");
            } else {
                cons.sendMessage("Copying is successfuly finished.");
            }
        } catch (IOException e) {
            if("move".equals(c)){
                cons.sendMessage("Moving failed!");
            } else {
                cons.sendMessage("Copying failed!");
            }
        }
    }
```
This method attempts to move a file's location.
If successful, it deletes the existing file and generates the file in a different location.
If "move" does not equal parameter c, it copies it instead.
Else it will tell you either condition faild based on the previous boolean expression.

```
 public void delete(String path){
        File file = new File(path);
        if(file.exists()){
            if(file.isFile()){
                file.delete();
                cons.sendMessage("File successfully deleted!");
            } else {
                deleteDir(file);
                cons.sendMessage("Folder successfully deleted!");
            }
        } else {
            cons.sendMessage("Cannot delete " + file.getName() + " because " + file.getName() + " does not exist on this path.");
        }
    }
```
This method deletes a file.

```
public void deleteDir(File f){
        File[] files = f.listFiles();
        if(files != null){
            for(File f1 : files){
                deleteDir(f1);
            }
        }
        f.delete();
    }
```
Deletes the contents of a file.

```
public void copyCutDir(File f1, File f2, String c) {
        if(!f2.exists()){
            f2.mkdir();
        }
        String fs[] = f1.list();
        for (String f : fs) {
            copyCutDir(new File(f1, f), new File(f2, f), c);
        }
        if("move".equals(c)){
            deleteDir(f1);
        }
    }
```
This is the helper method that is used in copyCut.
If the second file entered doesn't exist, it will delete the original and make a new one.
If it does, it copies that file.

```
 public void makeDir(boolean b, File f){
        if(b){
            f.mkdir();
        } else {
            f.mkdirs();
        }
    }
}
```
Creates a new directory.