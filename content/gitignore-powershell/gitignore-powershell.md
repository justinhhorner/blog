I create a lot of projects for instructional purposes, game jams, and prototypes and I find myself often going to [gitignore.io](https://www.gitignore.com) to grab the latest recommended Unity gitignore as a starting point.

![gitingore.io website]()

If you find yourself in a similar situation, you might find it more helpful to have access to gitignore.io via command line. In this post I'll go through how I make this possible on Windows via PowerShell. Let's get started.

## PowerShell Profile
If you're unfamiliar with PowerShell, understand that there is a special script called your `profile` that is automatically executed every time you start a PowerShell session and we can use it to customize our PowerShell environment. If you come from Linux or macOS, think of this as the PowerShell equivalent of a `.zshrc` or `.bashrc` file.

## Writing A New Function
Inside the profile, I added a new function called `Get-GitIgnore` that is capable of two things. First, if I call it to get the latest Unity `.gitignore` file, it will download the file and write it at the current `$PWD` location.

This is the recommended function from Toptal. I'll get into my customizations next.
```powershell
#For PowerShell v3
Function gig {
  param(
    [Parameter(Mandatory=$true)]
    [string[]]$list
  )
  $params = ($list | ForEach-Object { [uri]::EscapeDataString($_) }) -join ","
  Invoke-WebRequest -Uri "https://www.toptal.com/developers/gitignore/api/$params" | select -ExpandProperty content | Out-File -FilePath $(Join-Path -path $pwd -ChildPath ".gitignore") -Encoding ascii
}
```

The function can then ne used in a new PowerShell session like so:
```bash
gig unity
```

## Adding Preview Support
This is a great start, but this will always write a `.gitignore` file. Sometimes I just want to preview the files in the console before writing them out. Here's my updated version.

```powershell
Function Get-GitIgnore {
  param(
    [Parameter(Mandatory = $true)]
    [string[]]$list,

    [Parameter()]
    [bool]$Preview = $false
  )

  $params = ($list | ForEach-Object { [uri]::EscapeDataString($_) }) -join ","
  $uri = "https://www.toptal.com/developers/gitignore/api/$params"
  $content = Invoke-WebRequest -Uri $uri | Select-Object -ExpandProperty Content 

  if ($Preview) { 
      Write-Output $content
  }
  else {
     $filePath = Join-Path -Path $PWD -ChildPath ".gitignore"
     $content | Out-File -FilePath $filePath -Encoding ascii
  }
}
```
First, you'll notice I made some misc. updates to naming and capitalization, but let's focus on the preview changes. 

The new `$Preview` defaults to false so it works just like the original if no argument value is provided. Then I simply `Write-Output` if we pass `-Preview $true`, otherwise a file is created.

## Summary
Hopefully someone finds this helpful. Not a long post today but know that I'll be back to our usual game dev related content soon.

Until next time.

Take care.  
Stay awesome.
