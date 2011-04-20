$: << File.expand_path("../lib", __FILE__)
$: << File.expand_path("../vendor/multipart-post", __FILE__)

require "subdb/version"

task :default => [:test]

desc "Download fixture files for tests"
task :download_fixtures do
  unless File.exists?("test/fixtures/dexter.mp4")
    puts "Downloading dexter.mp4"
    `curl -o test/fixtures/dexter.mp4 http://thesubdb.com/api/samples/dexter.mp4`
  end

  unless File.exists?("test/fixtures/justified.mp4")
    puts "Downloading justified.mp4"
    `curl -o test/fixtures/justified.mp4 http://thesubdb.com/api/samples/justified.mp4`
  end

  puts "Done"
end

desc "Run tests"
task :test do
  $: << File.expand_path("../test", __FILE__)

  Dir["test/**/*_test.rb"].each do |test|
    require test
  end
end

desc "Build gem"
task :build do
  system "gem build subdb.gemspec"
end

desc "Release gem"
task :release => :build do
  system "gem push subdb-#{Subdb::VERSION}.gem"
end

desc "Build jar"
task :jar do
  unless File.exists?("vendor/jruby-complete-1.6.1.jar")
    puts "Downloading jruby-complete-1.6.1.jar, it may take a while..."
    `curl -o vendor/jruby-complete-1.6.1.jar http://jruby.org.s3.amazonaws.com/downloads/1.6.1/jruby-complete-1.6.1.jar`
  end

  `rm -rf jarbuild` if File.directory?("jarbuild")
  `mkdir -p jarbuild`

  puts "Copying files to jarbuild..."
  `cp -rf lib/* jarbuild`
  `cp -rf vendor/multipart-post/* jarbuild`

  Dir.chdir("jarbuild") do
    files = Dir.glob("./*")

    `cp ../vendor/jruby-complete-1.6.1.jar subdb.jar`
    `cp ../bin/subdb-gui jar-bootstrap.rb`

    puts "Adding files to jar..."
    `jar uf subdb.jar #{files.join(' ')}`

    puts "Setting up jar initialization..."
    `jar ufe subdb.jar org.jruby.JarBootstrapMain jar-bootstrap.rb`
  end

  jarname = "subdb-#{Subdb::VERSION}.jar"

  `rm #{jarname}` if File.exists?(jarname)
  `mv jarbuild/subdb.jar #{jarname}`
  `rm -rf jarbuild`

  puts "Done building #{jarname}"
end
