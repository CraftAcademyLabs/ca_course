



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



