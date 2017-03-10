$(document).ready(function(){

	var rowString = $('.movie-info').html();
	var buildRow = _.template(rowString);
	var back;

	var App = Backbone.Router.extend({
			routes:{
				'': 					'home',
				'search/:query':  		'search',
				'watch': 				'watch'
			},

			home: function(){
				$('.page').hide();
				$('h1').html('IMDB API');
				$('#home').show();
				$('h4').show();
				$('#watchLink').show();
				$('#searchLink').hide();
			},

			search: function(query){
				// $('h1').html('Results');
					$('#form').show();
					$('#search').show();
					$('.results').show();
					$('.watchlist').hide();
					$('#watchLink').show();
					$('#searchLink').hide();
					$('h3').show();
					$('h3').html('Search Results');

					$.get( 'http://www.omdbapi.com/?', {s: query}, function(moviesData){
			
						 var movies = moviesData.Search
						 $('input').val('');
						 $('.results').html('');
						 //add search results
						for(var i = 0; i< movies.length; i++){						
							var movie =  movies[i];						
							var addMovies = buildRow(movie);
							$('.results').append(addMovies);								
						}	
						//add title to watchlist
						$('.temp').each(function(){
							$(this).click(function(e){
								// console.log($('.watchlist').has('div').html())
								
								$(this).parent().css('opacity', '.5');
								var title = $(this).parent().find('.title').text()
								var year = $(this).parent().find('.year').text()
								$string = $('<div>'+ title +" "+ year +'</div>')
								//console.log($('.watchlist').text());	
								$('.watchlist').append($string.fadeIn('slow'));
							});
						});	
						//remove title from watchlist
						$('.watchlist').each(function(){
							$(this).click(function(e){
								 $(e.target).fadeOut('fast', function(){
								 	$(this).remove();
								 });
								
							})
						})			
					}
					,'json');
				},

				watch: function(){

					$('.results').hide();
					$('.watchlist').show();
					$('#watchLink').hide();
					$('#searchLink').show();
					$('h3').show();
					$('h3').html('Watch List');

				}

			
	});

		var myRouter = new App();
		Backbone.history.start();

		$('#form').submit(function(e){
			e.preventDefault();
			// console.log($('#input').val()); 
			query = $('#input').val();
			myRouter.navigate('search/'+ query, {trigger: true});
		});
		
		$('#watchLink').submit(function(e){
			e.preventDefault();
			myRouter.navigate('watch', {trigger: true});
		});
		$('#searchLink').click(function(e){
			e.preventDefault();
			window.history.back();
		});
	
		

});