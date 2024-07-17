
### Project 5: Fakebook Web Crawler

### High-Level Approach

For my high-level approach, I first started by logging into the server. To do this,
I had to manipulate the login form in such a way that I could extract the CSRF token
and the session cookie. Once I had these, I could then use them to log in. I stored the
cookie in a class variable so that I only needed to log in to Fakebook once, and could
include that cookie in the header of all future requests.

The next step I took from here after logging in was to extract the links of friend profiles
from the pages my crawler visits. I did this by creating a custom HTML parser and overrode its `handle_starttag` method
to extract the `href` attribute from all `a` tags on the page. In the same HTML parser,
I also overrode the `handle_data` method to extract the flags from the data that contains `FLAG:`
by splitting the data by `: ` and adding the second element to the list of flags (as 
that is a flag).

Once I had the links, I had to keep track of the frontier (which I used a deque for) and the
links that have already been visited (which I used a set for, because they can be iterated through
much easier than a queue can be iterated through). I then iterated through the links
and visited each one, extracting the links from each page and adding them to the frontier if they
had not already been visited. I also extracted the flags from each page and added them to a list.
I chose to print the flags out as my crawler goes instead of printing them all at the end.

I then implemented my handler for status codes. If the status code was a 200, I parsed the links
from the page as normaly and checked for flags. If the status code was a 302, I would extract the
location header (to find the redirect link) and add it to the frontier if it had not already been
visited, and would visit it later. If the status code was a 403 or 404, I would simply skip the
link and move on to the next one. If the status code was a 503, I would simply wait and
add the link to the end of the queue to visit later. For 500s and 504s, I would simply skip the
link because the error would be due to an internal server error or an infrastructure error.

### Challenges

The primary challenge I faced when completing this assignment was figuring out how to reverse-engineer 
the login form in order to log into Fakebook. I was able to find the CSRF middleware token
by simply inspecting the login form, but it took me a while to figure out what exactly to do with it
and how to use it alongside the session ID. Handling status codes was not too big of an issue for me,
but I also had trouble with figuring out which data structures to use for the frontier and the visited
set and how to update them correctly. I also had trouble with the HTML parser, as I had never used one
before and had to figure out how to use it correctly.

### Testing

Testing for this project took a lot of time. I created a logger (which can be visualized and activate
by using the -verbose option) to print the current runtime, the length of the queue, the amoun of flags found,
and the length of the visited set. Logging into the Fakebook server proved to be the most difficult, so to debug and
test this I printed out the CSRF token and the session ID first to make sure I could parse them correctly. I then
printed out the headers of the response to see that I was passing correct headers, and by printing out the raw response
and the headers I was able to parse the response easier. Overall, for testing, I printed out data at various points
in the code to make sure that I was extracting the correct data and used the printed responses with their status codes
to debug.