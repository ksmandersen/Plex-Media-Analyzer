#!/usr/bin/php
<?php

// ================================================== //
// EDIT HERE:										  //
// Please insert the IP Address or hostname of your	  //
// Plex Media Server.								  //
// ================================================== //
define('PLEX_URL', 'http://127.0.0.1:32400');



// ================================================== //
// Don't edit below this line
// ================================================== //

// Make sure we don't run out of RAM anytime soon.
ini_set('memory_limit', '512M');

// Make sure that we don't time out either
set_time_limit(6000);

/* Convenience wrapper class for file_get_contents
 * and simplexml_load_string functions.
 */
class XMLHandler
{
	/* Returns a XML string from the given URL. If the
	 * resource is unavailable or the content cannot be
	 * correctyl parsed as XML the method will return
	 * NULL.
	 */
	public static function getURL($url)
	{
		libxml_use_internal_errors(false);
		
		$data = file_get_contents($url);
		if($data == false) // Can't get resource
			return null;
			
		$xml = simplexml_load_string($data);
		if($xml == false) // Can't parse content
			return null;

		return $xml;
	}
}

/* Simple class for extracting TV Show and Movie statistics
 * from a Plex Media Server using the XBMC XML API. 
 *
 * ------
 * USAGE:
 * ------
 * $pma = new PlexMediaAnalyzer;
 * $pma->findContent();
 *
 * In order to print out statistics use:
 * $pma->doStatistics();
 *
 * -----
 * NOTE:
 * -----
 * The API used by this class has already been depricated and
 * replaced with JSON RPC API on the original XBMC project.
 *
 * This means that this class may not work with future versions
 * of Plex.
 */
class PlexMediaAnalyzer
{
	private $sections, $shows, $movies;
	private $is_verbose;
	
	public function __construct($args)
	{
		$this->sections = array();
		$this->shows = array();
		$this->movies = array();
		
		$this->is_verbose = (in_array('-v', $args) || in_array('--verbose', $args)) ? true : false;
	}
	
	public function findSections()
	{
		$xml = XMLHandler::getURL(PLEX_URL . '/library/sections');
		if(!$xml)
			return null;
		
		foreach($xml->Directory as $value)
		{
			$this->sections[] = array(
				'type' => (string) $value->attributes()->type, 
				'key' => (string) $value->attributes()->key
			);
		}
	}
	
	public function findContent()
	{
		if(!$this->sections || empty($this->sections))
			$this->findSections();
			
		foreach($this->sections as $section)
		{
			$xml = XMLHandler::getURL(PLEX_URL . '/library/sections/' . $section['key'] . '/all');
			if(!$xml)
				return null;
				
			if($section['type'] == 'show')
				$this->parseShowSection($xml);
			else if($section['type'] == 'movie')
				$this->parseMovieSection($xml);
		}
	}
	
	public function doStatistics()
	{
		if($this->is_verbose)
		{
			echo " ===========================\n";
			echo " TV SHOWS\n";
			echo " ===========================\n";	
		}
		
		$watched = 0;
		$total = 0;
		$seasons = 0;
		$duration = 0;
		
		foreach($this->shows as $title => $show)
		{	
			if($this->is_verbose)
			{
				echo " ---------------------------\n";
				echo " " . $title . "\n";
				echo " ---------------------------\n";	
			}
			
			$swatched = 0;
			$stotal = 0;
			$sseasons = 0;
			$sduration = 0;
			
			foreach($show->seasons as $season)
			{
				$sseasons++;
				foreach($season as $episode)
				{
					$stotal++;
					if($episode->count > 0)
					{
						$swatched++;
					}
					$sduration += $episode->duration * $episode->count;
				} // -foreach $episode
				
			} // -foreach $season
			
			$sunwatched = $stotal - $swatched;
			$shduration = round($sduration/3600000, 1);
			
			if($this->is_verbose)
			{
				echo " Seasons: $sseasons\n";
				echo " Episodes: $stotal\n";
				echo " Unwatched: $sunwatched\n";
				echo " ---------------------------\n";
			}
			
			$watched += $swatched;
			$total += $stotal;
			$seasons += $sseasons;
			$duration += $sduration;
		} // -foreach $show
		

		$hduration = round($duration/3600000, 1);
		$show_count = count($this->shows);
		if($this->is_verbose)
		{
			echo "\n\n";
			echo " ---------------------------\n";
			echo " Shows: $show_count\n";
			echo " Episodes: $total\n";
			echo " Watched: $watched ($hduration hours)\n";
			echo " ---------------------------\n";
			
			echo " ===========================\n";
			echo " Movies\n";
			echo " ===========================\n";	
		}
		
		
		$watched_movies = 0;
		$duration_movies = 0;
		foreach($this->movies as $key => $movie)
		{
			if($this->is_verbose)
				echo " " . $key . "\n";
				
			$duration_movies += $movie->count * $movie->duration;
			if ($movie->count > 0)
				$watched_movies++;
		}
		
		$hduration_movies = round($duration_movies/3600000, 1);
		$movie_count = count($this->movies);
		if($this->is_verbose)
		{
			echo "\n\n";
			echo " ---------------------------\n";
			echo " Movies: $movie_count\n";
			echo " Watched $watched_movies ($hduration_movies hours)\n";
			echo " ---------------------------\n";	
		}
		
		if(!$this->is_verbose)
		{
			echo "Found $movie_count movies and $total episodes (in $show_count shows)\n";
			echo "You have watched $watched_movies movies ($hduration_movies hours) and $watched episodes ($hduration hours)\n";
		}
		
	}
	
	private function parseMovieSection($xml)
	{
		foreach($xml->Video as $movie)
		{
			$title = (string) $movie->attributes()->title;
			$this->movies[$title]->duration = (integer) $movie->attributes()->duration;
			$this->movies[$title]->count = (integer) $movie->attributes()->viewCount;
		}
	}
	
	private function parseShowSection($xml)
	{
		$show_count = 0;
		$show_count_watched = 0;
	
		// Loop through all Shows in section
		foreach($xml->Directory as $show_dir)
		{
			
			if((string) $show_dir->attributes()->type == 'show')
			{
				$show_xml = XMLHandler::getURL(PLEX_URL . $show_dir->attributes()->key);
				
				// Loop through all Seasons in Show
				foreach($show_xml->Directory as $season_dir)
				{
					
					// Ignore the 'All episodes' category
					if((string) $season_dir->attributes()->type == 'season')
					{
						$season_xml = XMLHandler::getURL(PLEX_URL . $season_dir->attributes()->key);
						
						// The show title
						$title = (string) $show_xml->attributes()->parentTitle;
						
						// Create the show if it has not been yet
						if(!isset($this->shows[$title]))
						{
							$this->shows[$title]->year = (string) $show_xml->attributes()->parentYear;
							$this->shows[$title]->seasons = array();
						}
						
						// Extract the season number from the title
						$season_no = (string) trim(str_replace('season', '', strtolower($season_dir->attributes()->title)));
						
						$this->shows[$title]->seasons[$season_no] = array();
						
						// Loop through all Episodes in Season
						foreach($season_xml->Video as $episode_dir)
						{
							// If an episode has been found then update the shows
							// array to reflect the viewCount for that given episode
							if((string) $episode_dir->attributes()->type == 'episode')
							{
								$count = (isset($episode_dir->attributes()->viewCount)) ? (integer) $episode_dir->attributes()->viewCount : 0;
								$this->shows[$title]->seasons[$season_no][(integer) $episode_dir->attributes()->index]->count = $count;
								$this->shows[$title]->seasons[$season_no][(integer) $episode_dir->attributes()->index]->duration = (integer) $episode_dir->attributes()->duration;
							} // -if $type == episode
						
						} // -foreach $episode_dir
						
					} // -if type == season
					
				} // -foreach $season_dir
				
			} // -if type == show
			
		} // -foreach $show_dir
		
		
	} // #parseShowsection
}

array_shift($argv);

try
{
	$pma = new PlexMediaAnalyzer($argv);
	$pma->findContent();
	$pma->doStatistics();	
}
catch (Exception $e)
{
	echo "Error:\r\n", $e->getMessage(), "\r\n";	
}

?>