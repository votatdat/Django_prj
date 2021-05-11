# Some notes

- Data model: `django.db.models.Model`, models.py
- Default User model:  `django.contrib.auth.models.User`
- `slug`: this is a field intended to be used in URLs. A slug is a short label
   that contains only letters, numbers, underscores, or hyphens. 
- Timezone in Django: `django.utils.timezone`
- The Meta class inside the model contains metadata, [read more](https://stackoverflow.com/questions/10344197/how-does-djangos-nested-meta-class-work#:~:text=Meta%20inner%20class%20in%20Django,versions%20of%20the%20name%20etc.)
- Creating an administration site for models:
    - `django.contrib.admin` is already included in the `INSTALLED_APPS`
        setting
    - Basic setting: `admin.site.register(Post)` in admin.py, Post is the model
    - More advance setting:
    ```Python
    @admin.register(Post)
    class PostAdmin(admin.ModelAdmin):
        list_display = ('title', 'slug', 'author', 'publish', 'status')
        list_filter = ('status', 'created', 'publish', 'author')
        search_fields = ('title', 'body')
        prepopulated_fields = {'slug': ('title',)}
        raw_id_fields = ('author',)
        date_hierarchy = 'publish'
        ordering = ('status', 'publish')
    ```
- QuerySet: 
    - user = `User.objects.get(username='admin')`
    - post = `Post(title='Another post', slug='another-post', body='Post
      body.', author=user)`
    - `post.save()`
    - Retrieving objects: `all_posts = Post.objects.all()`
    - filter(): `Post.objects.filter(publish__year=2020) .filter(author__username='admin')`
    - exclude(): `Post.objects.filter(publish__year=2020).exclude(title__startswith='Why')`
    - order_by(): `Post.objects.order_by('-title')`
- Create Model manager:
  - Add extra manager methods to an existing manager: `Post.objects.my_manager()`
  - Create a new manager by modifying the initial QuerySet that the manager returns: ` Post.my_manager.all()`
    ```Python
    class PublishedManager(models.Manager):
        def get_queryset(self):
            return super(PublishedManager,
                         self).get_queryset()\
                              .filter(status='published')
  
    class Post(models.Model):
        # ...
        objects = models.Manager() # The default manager.
        published = PublishedManager() # Our custom manager.  
    ```
    - The manager will allow retrieving posts using `Post.published.all()`
