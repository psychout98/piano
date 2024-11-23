<%*
// Helper function, decides which templates to ignore.
// Adjust this function to match your setup.
const ignoreTemplate = name => 
	name.startsWith('Part ')
	|| name.startsWith('Script ');

// Auto-Detect the templates folder.
const plugins = app.setting.pluginTabs
	.filter(tab => 'Templater' === tab.name);
	
if (1 !== plugins.length) {
	new Notice('[Error] Could not find the Templater settings')
	return;
}

const templatesFolder = plugins[0].plugin.settings.templates_folder;

// Find all ".md" files inside the templates folder.
const templates = app.vault
	.getFiles()
	.filter(file => 'md' === file.extension
		&& file.path.startsWith(templatesFolder)
		&& ! ignoreTemplate(file.basename)
	)
	.sort((a, b) => a.name < b.name ? -1 : 1);

// Ask the user to select a template. ESC is also allowed.
const templateNames = templates
	.map((file, i) => `[${i+1}]  ${file.basename}`);
const useTemplate = await tp.system.suggester(
	templateNames,
	templates,
	false,
	'Choose a template for the file'
);

if (useTemplate) { 
	%><% tp.file.include(useTemplate) %><%*
}
