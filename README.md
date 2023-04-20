# UpdateAPI-Publish
Приложение сделано для удобного создания файла с версиями приложения.

![image](https://user-images.githubusercontent.com/76705837/233179296-806440db-87a6-4e64-9081-2ba8c2608cf5.png)


При добавлении файлов версии, создайте папку которая будет идентична наименованию папки на гитхабе.
![image](https://user-images.githubusercontent.com/76705837/233179477-2a73f125-a36c-4a8b-a6e5-587ec8ff7adc.png)

Как показано ниже создана папка 1.0.
![image](https://user-images.githubusercontent.com/76705837/233179702-543bc507-de81-4745-b3d7-152ae9df74fb.png)

![image](https://user-images.githubusercontent.com/76705837/233179499-21d71430-1aac-4f4a-913d-e4911f796870.png)

Пример кода для получения обновлений с текущего репозитория.

```cs
UpdaterAPI.GitHub.Downloader downloader = new UpdaterAPI.GitHub.Downloader();
    downloader.SetRootPath(Directory.GetCurrentDirectory());
    downloader.SetUrlUpdateInfo("Camyil-89/UpdateAPI-Publish/main/UpdateInfo.xml");
    downloader.SetUrlDowloadRoot("Camyil-89/UpdateAPI-Publish/main/versions");
    var tmp_path = $"{Directory.GetCurrentDirectory()}\\update";
    var last_version = downloader.GetLastVerison(UpdaterAPI.Models.TypeVersion.Release);

	if (last_version.Version != Settings.Instance.Version && MessageBoxHelper.QuestionShow($"Доступна новая версия {last_version.Version}\nСкачать?") == MessageBoxResult.Yes)
	{
		App.Current.Dispatcher.Invoke(() =>
		{
			var status = UpdateAPIUI.UIProvider.DowloadFilesWithInstaller(downloader, last_version, tmp_path);
			if (status == UpdateAPIUI.Models.StatusDowload.OK)
			{
				downloader.CopyFilesFromTempDirectory(tmp_path, $"taskkill /pid {Process.GetCurrentProcess().Id} &&", $"&& rmdir /s /q \"{Directory.GetCurrentDirectory()}\\update\" && \"{Process.GetCurrentProcess().MainModule.FileName.Split("\\").Last()}\"");
			}
			else
			{
				Directory.Delete(tmp_path, true);
			}
		});
	}
```
