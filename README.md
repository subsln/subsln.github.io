# `.subsln` file format

`.subsln` file format is used by [SubSolution tools](https://github.com/ReMinoer/SubSolution) to describe the content of Visual Studio solutions with a more user-friendly syntax than the `.sln` format. You can generate a Visual Studio solution from it or update an existing one.

```xml
<SubSolutionConfiguration xmlns="http://subsln.github.io/">
    <Root>
        <Folder Name="Tools">
            <Files Path="tools/*.bat" />
        </Folder>
        <Folder Name="Tests">
            <Projects Path="**/*.Tests.csproj" />
        </Folder>
        <Projects Path="src/">
    </Root>
</SubSolutionConfiguration>
```

# Why use a `.subsln` file ?

- It allows you to __express your organization rules__ ("those projects in that folder, unit tests in that one...") and __ensure they are respected__ on solution changes.
- It acts as a __substitute or edition assistant__ of .sln files, to describe the solution content with a __user-friendly structure__ similar to Visual Studio representation.
- It can also be used as a punctual tool, to __apply a one-time update__.
- It allows to __quickly iterate__ on your solution structure until it matches your needs, without requiring to run Visual Studio.
- It can __build an entirely customized hierarchy__, or at contrary __mirror your file system structure__.
- It can __find and fill your solution with dependencies__ of your central projects.
- It can describe solutions __in a modular way__ by including the content of a solution into another.
- It can __apply changes to multiple solutions__ sharing the same projects.
- It can __divide a big solution in smaller ones__ to reduce impact on Visual Studio performances.

# XML Syntax

- Describe your item hierarchy: `<Root>`
    - Create folders to organize your items: `<Folder>`
    - Add projects with glob patterns: `<Projects>`
    - Add files for quick-access: `<Files>`
    - Find project dependencies and dependents: `<Dependencies>` / `<Dependents>`
    - Include the content of existing solutions: `<Solutions>` / `<SubSolutions>`
    - Select what you want to keep from other solutions: `<KeepOnly>`
    - Apply complex filters on your item sources: `<Where>`
- Setup your solution configuration-platforms: `<Configurations>` / `<Platforms>`
    - Ignore them to auto-generate from projects.
    - Create new ones: `<SolutionConfiguration>` / `<SolutionPlatform>`
    - Match them with project configurations and platforms: `<ProjectConfiguration>` / `<ProjectPlatform>`
- And a lot more options as XML attributes !

You can find the [XML schema here](https://github.com/ReMinoer/SubSolution/blob/master/Sources/SubSolution.Builders/SubSolutionConfiguration.xsd).
