# grunt-svn-custom-tag

Creates custom versioned tag entries in your SVN repository. If you want to do a brute-force snapshot of your project i.e. put your whole project into a tag, then you probably want to use [grunt-svn-tag](https://www.npmjs.com/package/grunt-svn-tag). However, if you want to pick and choose which files from your project are tagged, then this is the plugin for you.

## Getting Started
This plugin requires Grunt `~0.4.0`

If you haven't used [Grunt](http://gruntjs.com/) before, be sure to check out the [Getting Started](http://gruntjs.com/getting-started) guide, as it explains how to create a [Gruntfile](http://gruntjs.com/sample-gruntfile) as well as install and use Grunt plugins. Once you're familiar with that process, you may install this plugin with this command:

	npm install grunt-svn-fetch --save-dev

One the plugin has been installed, it may be enabled inside your Gruntfile with this line of JavaScript:

	grunt.loadNpmTasks('grunt-svn-fetch');

## Overview

This plugin requires some user input to determine the right version number to use. However, this can also be circumvented by supplying arguments from the command-line (see below).

## Options

### options.defaultBump

Type: `String`

Default: `'z'`

When prompted to select the bump level, pressing enter will select the default bump. See below for more details.

### options.latest

Type: `boolean` | `string`

Default: `true`

The plugin provides the means to copy the bumped version to a `latest` tag folder. Whether or not this is done is determined by the value of this option.

Set this to `true` to copy to the latest without prompting. Set to `false` to skip this step. Set to `'prompt'` to be asked at run-time.

### options.repository

Type: `String`

Default: none

Specifies the project's SVN repository URL. This must be specified or the task will fail.

### options.tagDir

Type: `String`

Default: `'tags'`

Specifies the root tag folder in the project's SVN respository.

## Version Numbering

The plugin adheres to the [Semver](http://semver.org/) system of version numbering, and utilises the [semver](https://docs.npmjs.com/misc/semver) npm package to achieve this.

## Tagged Folder Structure

The assumption is that all folders under the `tagDir` folder are versioned (i.e. the name is the version number). When creating a new snapshot, a new folder under the tag root will be added, and the specified files imported. See below for an example.

## Specifying Custom Tagging

The files and folders you want tagged can be specified in the normal manner, by supplying a `files` configuration object (see example above). The `src` component identifies the files from your working copy that will be imported into the tag folder. The `dest` component specifies the folder under the create version folder to which the source files/folders will be imported. For example:

	svn_custom_tag: {
		options: {
			respository: 'http://svn.my_company.com/my_project'
		},
		my_project: {
			files: [
				{
					src: '/my/project/path/dist/*.js',
					dest: 'dist'
				}
			]
		}
	}

Assuming a fix-bump, and our latest tagged version was 1.2.3, the new version tag is 1.2.4, and the repository would be updated as follows:

	http://svn.my_company.com/my_project
		/tags
			...
			/1.2.4
				/dist						<--- specified by dest
					my_dist_file.min.js		<--- specified by src

Note that multiple file object can be specified, and each will be processed in turn.

## User Input

In order to determine the next bump type, the user is asked the nature of the change. Possible responses (all case-insensitive) are:

1. 'X' - A major change, i.e. 2.1.15 => 3.0.0
2. 'Y' - A minor change, i.e. 2.1.15 => 2.2.0
3. 'Z' - A patch change, i.e. 2.1.15 => 2.1.16
4. 'PX' - A premajor change, i.e. 2.1.15 => 3.0.0-0
5. 'PY' - A preminor change, i.e. 2.1.15 => 2.2.0-0
6. 'PZ' - A prepatch change, i.e. 2.1.15 => 2.1.16-0
7. 'E' - An explicit version. The user is asked to supply the version directly.
8. 'Enter' - Use the `defaultBump` option. For convenience.
9. 'Q' - Quit the task.

### Avoiding User Input

It is possible to circumvent having to supply user input by providing the bump type as the first argument for the task. For example:

	> grunt svn_custom_tag:my_project:pz

In this case, only the verions bumps (1-6 above) are recognised. This feature allows for custom tasks to be defined, which can be invoked as required, e.g.

	grunt.registerTask('tag_fix', [ 'svn_custom_tag:my_project:z' ])

Lovely jovely!
