date:: 2024-02-18
keywords:: development, javascript, graph
public:: true

- ### Project goals:
	- Create an interactive "graph view" of this website to display on its homepage.
	- Make it compatible with github pages and hugo's static site specifications.
- ### Research:
	- [Plotly with Hugo](https://mertbakir.gitlab.io/hugo/plotly-with-hugo/)
	- [Logseq codebase overview](https://github.com/logseq/logseq/blob/master/CODEBASE_OVERVIEW.md)
	- [Article on logseq codebase structure](https://docs.logseq.com/#/page/The%20Refactoring%20Of%20Logseq)
	- [Logseq built-in renderer](https://github.com/logseq/logseq/blob/master/src/main/frontend/extensions/graph/pixi.cljs)
- ### Development
	- `themes/PaperModlayouts/partials/home_info.html` is the template for my home page
		- I need to figure out how to render a graph in this page
		- JavaScript parser returns map containing all links
			- ```javascript
			  import fs from 'fs';
			  import path from 'path';
			  
			  // Directory to search 
			  const dir = String.raw`S:\Projects\tealblu.github.io\content\pages`;
			  
			  // Regular expression to match
			  const regex = /\[([^\]]*)\]\(\{\{/gm; 
			  
			  // Map to store matches
			  let map = new Map();
			  
			  // Recursive function to search directories
			  function searchDirs(dir) {
			      // Read files in directory
			      fs.readdirSync(dir).forEach(file => {
			      const filePath = path.join(dir, file);
			  
			      // Check if file is directory
			      if (fs.statSync(filePath).isDirectory()) {
			          // Search subdirectory
			          searchDirs(filePath);
			      } else {
			          // Check file content for match
			          const content = fs.readFileSync(filePath, 'utf8');
			          if (regex.test(content)) {
			              // console.log("File: " + filePath);
			  
			              const matches = content.match(regex);
			              
			              // Add matches to map
			              const match_array = new Array();
			              if (matches.length > 0) {
			                  // loop through matches and remove lead ing [ and trailing ]({{
			                  matches.forEach(match => {
			                      const match_str = match.substring(1, match.indexOf(']'));
			                      match_array.push(match_str);
			                  });
			                  map.set(file, match_array);
			              }
			          }
			      }
			      });
			  }
			  
			  // Start search in directory, return {filepath: match} map
			  searchDirs(dir);
			  console.log(map);
			  ```
		- Map contents:
			- ```
			  Map(5) {
			    '2024-01-20.md' => [ 'Graph Structure', 'Deployment', 'Templates', 'Projects Homepage' ],
			    '2024-01-21.md' => [ 'Code Projects', '2024-01-21' ],
			    '2024-02-18.md' => [ 'Graph Structure' ],
			    'Code Projects.md' => [
			      'PDP-11 Instruction Set Simulator',
			      'DriveTime',
			      'Image Classification AI',
			      'LUCA'
			    ],
			    'Projects Homepage.md' => [ 'Code Projects' ]
			  }
			  ```
		- TODO Remember how to render a d3 image
		- TODO Write script to render map as linked-node tree