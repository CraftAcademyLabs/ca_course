Respond to different formats

```ruby
  def show
    @article = Article.find(params[:id])

    respond_to do |format|
      format.json { render json: @article }
      format.html { @article }
    end
  end
```



### Serializing data

[Active Model Serializers](https://github.com/rails-api/active_model_serializers) is probably the most used library in this field and provides you with a relatively simple setup for serializing your resources into custom JSON. You can define multiple serializers for the same resource with different contents, serialize associated records, use different output formats like attributes list serialization and much more.

If you’ll need to be fully compliant with the JSON API spec you may want to consider [JSONAPI::Resources](https://github.com/cerebris/jsonapi-resources). The serialization setup is straightforward, and in addition, you can have your controllers accepting requests in JSON API format to perform CRUD operations. 🎉

Recently, Netflix released their own JSON API serialization mechanism, [Fast JSON API](https://github.com/Netflix/fast_jsonapi). As its name suggests, they’re aiming at being fast 😅 and so far the results are promising, with a 25x quicker solution when compared with ActiveModelSerializers, as per their benchmark. Well done! 👏



Testing your API endpoints

https://github.com/jsonapi-rb/jsonapi-rspec

