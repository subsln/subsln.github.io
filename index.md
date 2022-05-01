# .Subsln files

.Subsln files are used by [SubSolution tools](https://github.com/ReMinoer/SubSolution) to describe the content of Visual Studio solutions in a user-friendly syntax. You can generate a Visual Studio solution from it or update an existing one.

You can find the [XML schema here](https://github.com/ReMinoer/SubSolution/blob/master/Sources/SubSolution.Builders/subsln.xsd).

```xml
<Subsln xmlns="http://subsln.github.io/">
    <Root>
        <Folder Name="Tools">
            <Files Path="tools/*.bat" />
        </Folder>
        <Folder Name="Tests">
            <Projects Path="**/*.Tests.csproj" />
        </Folder>
        <Projects Path="src/">
    </Root>
</Subsln>
```

## Why use a .subsln file ?

- It allows you to __express your solution organization rules__ ("those projects in that folder, unit tests in that one...") and ensure they are respected on solution changes.
- It acts as a __substitute or edition assistant__ of .sln files, to describe the solution content with a user-friendly structure similar to the Visual Studio "Solution Explorer" representation.
- It can also be used as a punctual tool, to __apply a one-time update__.
- It allows to __quickly iterate__ on your solution structure until it matches your needs, without requiring to run Visual Studio.
- It can __build an entirely customized hierarchy__, or at contrary mirror your file system structure.
- It can __find and fill your solution with dependencies__ of your central projects.
- It can describe solutions __in a modular way__ by including the content of a solution into another.
- It makes it easier to __apply changes to multiple solutions__ sharing the same projects.
- It can __create a smaller solution from a big one__ to have a better environment or performances in Visual Studio.

# Subsln

By default, a .subsln file will generate a .sln file in the same folder and use the same filename. But paths and names can be configured through optional attributes:

```xml
<Subsln xmlns="http://subsln.github.io/"
        SolutionName="MySolution"
        OutputDirectory="sources/solutions"
        WorkspaceDirectory="sources/projects">
    ...
</Subsln>
```

- `SolutionName` **(Optional)**
    - Filename of the generated .sln file.
    - If not provided, it will use the filename of the .subsln file.
- `OutputDirectory` **(Optional)**
    - Output directory for the generated .sln file.
    - If not provided, it will use the directory containing the .subsln file.
- `WorkspaceDirectory` **(Optional)**
    - Root directory used to find solution items.
    - If not provided, it will use the directory containing the .subsln file.

# Items sources

You can add items sources to a `<Root>` element in `<Subsln>` to define your solution items and hierarchy.

There are multiple item sources:

- `<Projects>`: Add projects
- `<Files>`: Add files
- `<Folder>`: Add a solution folder. **Can contains other item sources.**
- `<Dependencies>`: Add the dependencies of a project set.
- `<Dependents>`: Add the dependents of a project set.
- `<Solutions>`: Add the content of an existing solution.
- `<SubSolutions>`: Add the content resulting of the build of others SubSolution files.

## Projects

```xml
<Projects />
<Projects Path="sources/projects/MyProject.csproj" />
<Projects Path="sources/projects/" />
<Projects Path="sources/projects/**/*.csproj" />
```

- `Path` **(Optional)**
    - Path used to find projects. Can be a single project path or a file glob pattern.
    - If the pattern has no extensions, it will match popular projects extensions.
    - If the pattern represents a directory, it will match all the projects in that directory.
    - If no pattern is provided, it will match all the projects in the workspace directory and its children.

## Files

```xml
<Files Path="tools/deploy.bat" />
<Files Path="tools/" />
<Files Path="tools/**/*.bat" />
```

- `Path` **(Optional)**
    - Path used to find projects. Can be a single project path or a file glob pattern.
    - If the pattern has no extensions, it will match any extensions.
    - If the pattern represents a directory, it will match all the files in that directory.
    - If no pattern is provided, it will match all the files in the workspace directory and its children.

## Folder

```xml
<Folder Name="Projects">
    <Folder Name="Test Projects">
        <Projects Path="**/*.Test.csproj" />
    </Folder>
    <Projects />
</Folder>
```

- `Name` **(Mandatory)**
    - Name of the solution folder.
- Children item sources will add their items in the folder.

## Dependencies

```xml
<Dependencies />
<Dependencies Target="ProjectSetID" />
```

- `Target` **(Optional)**
    - ID of the project set we want the dependencies.
    - If not provided, it will get the dependencies of all the projects already added to the solution at this point of the build.

> **Example**: Add an application project and all its dependencies.
>
>```xml
><Projects Path="sources/projects/MyApplication.csproj" />
><Dependencies />
>```

## Dependents

```xml
<Dependents />
<Dependencies Target="ProjectSetID" Scope="ScopeProjectSetID"
    KeepOnlyDirect="true" KeepOnlySatisfied="true" />
```

- `Target` **(Optional)**
    - ID of the project set we want the dependent projects.
    - If not provided, it will get the dependencies of all the projects already added to the solution at this point of the build.
- `Scope` **(Optional)**
    - ID of the project set in which search for dependent projects.
    - If not provided, it will search among all the projects contained in the workspace directory and all its children.
- `KeepOnlyDirect` **(Optional)**
    - True to keep only the direct dependents.
    - False by default.
- `KeepOnlySatisfied` **(Optional)**
    - True to only add projects whose dependencies will be satisfied with already added + currently resolved dependent projects.
    - **Applied after filter.**
    - False by default.
- `KeepOnlySatisfiedBeforeFilter` **(Optional)**
    - True to only add projects whose dependencies will be satisfied with already added + currently resolved dependent projects.
    - **Applied before filter.**
    - False by default.

## Solutions

```xml
<Solutions />
<Solutions Path="solutions/MySolution.sln" />
<Solutions Path="solutions/" />
<Solutions Path="solutions/**/*.sln" />
```

- `Path` **(Optional)**
    - Path used to find solutions. Can be a single solution path or a file glob pattern.
    - If the pattern has no extensions, it will use the .sln extension.
    - If the pattern represents a directory, it will match all the solutions in that directory.
    - If no pattern is provided, it will match all the solution in the workspace directory and its children.
- `CreateRootFolder` **(Optional)**
    - True to add the solution content in a root folder using the solution name.
    - False by default.
- `ReverseOrder` **(Optional)**
    - True to treat the solution files contained in the depths of the file hierarchy before the ones contained in the parent folders.
    - Useful to list solutions even if they have a recursive content.
    - False by default.

## SubSolutions

```xml
<SubSolutions />
<SubSolutions Path="solutions/MySolution.subsln" />
<SubSolutions Path="solutions/" />
<SubSolutions Path="solutions/**/*.subsln" />
```

- `Path` **(Optional)**
    - Path used to find solutions. Can be a single solution path or a file glob pattern.
    - If the pattern has no extensions, it will use the .subsln extension.
    - If the pattern represents a directory, it will match all the solutions in that directory.
    - If no pattern is provided, it will match all the solution in the workspace directory and its children.
- `CreateRootFolder` **(Optional)**
    - True to add the solution content in a root folder using the solution name.
    - False by default.
- `ReverseOrder` **(Optional)**
    - True to treat the solution files contained in the depths of the file hierarchy before the ones contained in the parent folders.
    - Useful to list solutions even if they have a recursive content.
    - False by default.

# Advanced options

## Build order & Overwrite

During solution, all the items sources will be applied to solution, **in the order**. If an item has already been added to solution by a previous item source, it will not be modified.

To force an items source to redefine an item already added to solution by a previous source, you can set the attribute `Overwrite` to `true`.

It can be necessary to move some projects in the wanted folder.

>**Example**: Create a copy an existing solution but move the tests projects in a specific solution folder.

```xml
<Solutions Path="MySolution.sln" />
<Folder Name="Tests">
    <Projects Path="**/*.Tests.csproj" Overwrite="true" />
<Folder />
```

## Folders mirroring

Use attribute `CreateFolders` on any project or file source to recreate the file system hierarchy inside the solution.

If you have too many intermediary folder in result, you can use `CollapseFoldersWithUniqueSubFolder` and `CollapseFoldersWithUniqueItem` on your root or folder to clean unnecessary folders.

>**Example**: Get all projects and recreate their folder organization, but remove all folder with a unique project or sub-folder.

```xml
<Root CollapseFoldersWithUniqueSubFolder="true" CollapseFoldersWithUniqueItem="true">
    <Projects CreateFolders="true" />
</Root>
```
>**Note**: `Collapse` attributes are applied recursively in all sub-folders. You can stop the propagation of the collapsing behaviour by setting it back to `false` in the wanted sub-folder.

## Project sets & Virtual

To declare a project set, you can give an `Id` to a project source. All the projects resolved by that source will be stored under that name.

You can then use them as `Target` or `Scope` of `<Dependencies>` and `<Dependents>` item sources.

If you want to define a set of projects without adding them to the current solution, you can put project sources in the `Virtual` element instead of `Root`. Virtual item sources are resolved at build start to prepare the project sets you will need during build.

>**Example**: Get only dependents projects that can be found in another solution.

```xml
<Virtual>
    <Solutions Path="MySolution.sln" Id="SolutionContent" />
</Virtual>
<Root>
    <Projects Path="MyFramework*.csproj" Id="Framework" />
    <Dependents Target="Framework" Scope="SolutionContent" />
</Root>
```

## Where

You can use advanced filters with the element `<Where>` inside the item sources when the file glob pattern is not enough:

- `<Projects>` / `<Dependencies>` / `<Dependents>`
    - `<Where>` using **projects filters** on projects.
- `<Files>`
    - `<Where>` using **file filters** on files.
- `<Solutions>` / `<SubSolutions>`
    - `<Where>` using **file filters** on solutions.
    - `<WhereProjects>` using **projects filters** on contained projects.
        - Set attribute `IgnoreAll` to `true` to ignore all projects.
    - `<WhereFiles>` using **file filters** on contained files.
        - Set attribute `IgnoreAll` to `true` to ignore all files.

### Project filters

- `<ProjectPath Match="**/*.csproj" />`: keep projects matching the file glob pattern.
- `<ProjectNot>`: keep projects not matching the contained filter.
- `<ProjectMatchAll>`: keep projects matching all the contained filters.
- `<ProjectMatchAnyOf>`: keep projects matching any of the contained filters.

### File filters

- `<FilePath Match="**/*.txt" />`: keep projects matching the file glob pattern.
- `<FileNot>`: keep projects not matching the contained filter.
- `<FileMatchAll>`: keep projects matching all the contained filters.
- `<FileMatchAnyOf>`: keep projects matching any of the contained filters.

>**Example**: Get only test projects from solutions not in "external" folder.

```xml
<Solutions />
    <Where>
        <FileNot>
            <FilePath Match="external/">
        </FileNot>
    </Where>
    <WhereProjects>
        <ProjectMatchAnyOf>
            <ProjectPath Match="**/*Test.csproj">
            <ProjectPath Match="**/*Tests.csproj">
        </ProjectMatchAnyOf>
    </WhereProjects>
    <WhereFiles IgnoreAll="true" />
</Solutions>
```

## KeepOnly

On `<Solutions>` and `<SubSolutions>`, you can also use item sources in the `<KeepOnly>` element to filter items.

In that case, only the items found by both the original item source and by any of  `<KeepOnly>` item sources will be kept. 
It's useful to filter items of an existing solution by dependency relationship or solution content, without breaking the original solution structure.

>**Example**: Get projects (and their folders) from another solution only if they are dependent of a given project.

```xml
<Projects Path="**/MyFramework.csproj" Id="Framework" />
<SubSolutions Path="MySolution.sln" />
    <KeepOnly>
        <Dependents Target="Framework" />
    </KeepOnly>
</Solutions>
```

# Configurations & Platforms

By default, the configurations and platforms of the resulting solution are automatically defined and filled by the projects contained in the solution.

But it is possible to define your own solution configurations and platforms, and decide how they match the projects configurations and platforms.

- `<Configurations>`/`<Platforms>` **(Optional)**
    - If not defined, solution configurations/platforms are automatically filled from project configurations/platforms.
    - `<Configuration>`/`<Platform>` children
        - `Name` **(Mandatory)**
            - Name of the solution configuration/platform.
        - `<ProjectConfiguration>`/`<ProjectPlatforms>` children
            - If no children defined, the solution configuration/platform will only match projects using the same configuration/platform name.
            - `Match` **(Mandatory)**
                - Text to match in the project configuration/platform name.

>**Note**: Like in Visual Studio, the popular project platform "AnyCPU" is renamed as "Any CPU" in solutions.

>**Example**: Solution with 3 manually-defined solution configurations and 3 solution platforms.

```xml
<Subsln xmlns="http://subsln.github.io/">
    <Root>
        <Projects />
    </Root>
    <Configurations>
        <!-- 3 configurations: Debug, Release, Final -->
        <!-- Final build projects in Final, or fallback on Release. -->
        <Configuration Name="Debug" />
        <Configuration Name="Release" />
        <Configuration Name="Final">
            <ProjectConfiguration Match="Final" />
            <ProjectConfiguration Match="Release" />
        </Configuration>
    </Configurations>
    <Platforms>
        <!-- 3 configurations: Any CPU, x86, x64 -->
        <!-- x86/x64 build projects in x86/x64, or fallback on Any CPU. -->
        <Platform Name="Any CPU" />
        <Platform Name="x86">
            <ProjectPlatform Match="x86" />
            <ProjectPlatform Match="Any CPU" />
        </Platform>
        <Platform Name="x64">
            <ProjectPlatform Match="x64" />
            <ProjectPlatform Match="Any CPU" />
        </Platform>
    </Platforms>
</Subsln>
```
