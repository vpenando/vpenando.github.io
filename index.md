## Welcome here! | [My GitHub projects](projects.md) | [About me](about.md)
I'm a C++ and C# developer, and this is my GitHub dev blog!

<img src="assets/cat-clap.gif" alt="hi" class="inline"/>

## Articles
*Here is -or will be- my articles list.*

*(coming soon!)*
<div id="articlesList">
</div>
    
<script>
var articles = [];

function addArticle(title, link){
  articles.push({"title": title, "link": link});
  document.getElementById('articlesList').innerHTML += '<li><a href="' + link + '">' + title + '</a></li>';
}
</script>

<script>
document.getElementById('articlesList').innerHTML += "<ul>";
addArticle("C# - Let's create an ECS!", "articles/creating_ecs_csharp.md");

document.getElementById('articlesList').innerHTML += "</ul>";

</script>
