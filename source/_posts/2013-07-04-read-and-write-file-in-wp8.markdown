---
author: onmyway133
comments: true
date: 2013-07-04 16:11:49+00:00
layout: post
slug: read-and-write-file-in-wp8
title: Read and write file in WP8
wordpress_id: 448
categories:
- Windows Phone
tags:
- file
- folder
- manipulation
- read
- tutorial
- windows phone 8
- wp8
- write
---

This tutorial will show you some code snippets that perform file manipulation in WP8




### Using .NET APIs




**Read file from Installation folder**  

The installation folder is the read-only folder that contains the files of your app package.




[code language="csharp"]  

private async Task ReadFileFromInstallationFolder()  

        {  

            // Get installed location  

            StorageFolder folder = Windows.ApplicationModel.Package.Current.InstalledLocation;




           // Get fileStream  

            Stream fileStream = await folder.OpenStreamForReadAsync("hello.txt");




           // Read the data.  

            using (StreamReader streamReader = new StreamReader(fileStream))  

            {  

                string s = streamReader.ReadToEnd();




               MessageBox.Show(s);  

              }  

        }  

[/code]




Also, you can use _GetFileFromApplicationUriAsync_ to get a specific file from Installation Folder. Remember to use "ms-appx" as we are using Windows namespace




[code language="csharp"]  

private async Task ReadFileFromInstallationFolder2()  

        {  

            StorageFile file = await Windows.Storage.StorageFile.GetFileFromApplicationUriAsync(new Uri("ms-appx:///hello.txt"));




           // Read the data.  

            using (StreamReader streamReader = new StreamReader(await file.OpenStreamForReadAsync()))  

            {  

                string s = streamReader.ReadToEnd();




               MessageBox.Show(s);  

            }  

        }  

[/code]




**Read file from Local Folder**  

The local folder is the root folder of your app’s data store




[code language="csharp"]  

private async Task ReadFileFromLocalFolder()  

        {  

            // Get the local folder.  

            StorageFolder local = Windows.Storage.ApplicationData.Current.LocalFolder;




           if (local != null)  

            {  

                // Get the MyFolder folder.  

                var dataFolder = await local.GetFolderAsync("MyFolder");




               // Get the file stream  

                var fileStream = await dataFolder.OpenStreamForReadAsync("hello.txt");




               // Read the data.  

                using (StreamReader streamReader = new StreamReader(fileStream))  

                {  

                    string s = streamReader.ReadToEnd();




                   MessageBox.Show(s);  

                }




           }  

        }  

[/code]




**Write file to Local Folder**




[code language="csharp"]  

private async Task WriteFileToLocalFolder()  

        {  

            // Get the text data from the textbox.  

            byte[] fileBytes = System.Text.Encoding.UTF8.GetBytes(this.tbText.Text.ToCharArray());




           // Get the local folder.  

            StorageFolder local = Windows.Storage.ApplicationData.Current.LocalFolder;




           // Create a new folder name MyFolder.  

            var dataFolder = await local.CreateFolderAsync("MyFolder",  

                CreationCollisionOption.OpenIfExists);




           // Create a new file named DataFile.txt.  

            var file = await dataFolder.CreateFileAsync("hello.txt",  

            CreationCollisionOption.ReplaceExisting);




           // Write the data from the textbox.  

            using (var s = await file.OpenStreamForWriteAsync())  

            {  

                s.Write(fileBytes, 0, fileBytes.Length);  

            }  

        }  

[/code]




You can also use _BinaryReader_ and _BinaryWriter_ to process binary files




### Using C++/CX APIs




**Read and write file in Local Folder**




[code language="cpp"]  

#include <stdio.h>  

#include <windows.h>  

#include <fileapi.h>  

#include <share.h>




void WindowsPhoneRuntimeComponent::ReadFileFromInstallationFolder()  

{  

 Platform::String^ localfolder = Windows::Storage::ApplicationData::Current->LocalFolder->Path;  

 Platform::String^ myFileName = Platform::String::Concat(localfolder, "\\myfile.txt");




FILE* pFile = _wfsopen(myFileName->Data(), L"wt", _SH_DENYNO);  

 if (pFile!=NULL)  

 {  

  fputs ("fopen example", pFile);




 fclose (pFile);  

 }




// read  

 pFile = _wfsopen(myFileName->Data(), L"rt", _SH_DENYNO);  

 char buf[100];  

 if(pFile != NULL)  

 {  

  fgets(buf, 100, pFile);  

 }  

}  

[/code]




**Read file from Installation Folder**  

The routine is the same, except that you must get the Installation Folder path




[code language="cpp"]  

Platform::String^ installedLocation = Windows::ApplicationModel::Package::Current->InstalledLocation->Path  

[/code]
