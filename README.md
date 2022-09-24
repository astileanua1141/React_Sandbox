# React_Sandbox
A collection of Microservices projects based on React, created through courses and learning materials. 

## #1: 1-small-react-blog
Components: 
#### client
	A React base app, containing some pages that allow some calls to the 2 services: posts and comments.
	imports: axios
	uses: Bootstrap CSS for styling

#### posts 
	A tiny service that creates and retrieves Blog Posts 
	imports: express cors axios nodemon
		- GET /posts
		- POST /posts { title }

#### comments
	Another tiny service that keeps track of Comments made on Blog Posts
	imports: express cors axios nodemon
	methods: 
		- GET /posts/:id/comments
		- POST /posts/:id/comments { content }

