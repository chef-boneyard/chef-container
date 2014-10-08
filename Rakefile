require 'rake'
require 'erb'
require 'tmpdir'

@latest_version = '11.16.2'

namespace :docker do
  desc 'build specified image'
  task :build, :image_name, :version do |t, args|
    image_name = args[:image_name] || 'ubuntu-12.04'
    version = args[:version] || @latest_version
    is_latest = version == @latest_version

    version_tags = []
    version_tags << version
    version_tags << version.split('.')[0,2].join('.')
    version_tags << version.split('.')[0,1].join('.')
    version_tags << 'latest' if is_latest

    puts "Rendering Dockerfile for #{image_name}:#{version}..."
    render_dockerfile(image_name, version)

    puts "Dockerfile rendered. Building Docker image..."
    output = %x{docker build #{tempdir}}
    puts output
    image_id = output.match(/^Successfully built ([a-zA-Z0-9]+)$/)[1]

    # Verify to make sure chef-init is installed correctly
    puts "Image #{image_id} build complete. Verifying build..."
    pass = %w{docker run --rm #{image_id} chef-init --verify}
    unless pass
      puts "FATAL: Verification failed!"
      exit
    else
      puts "Build passed."
    end

    # Tag the image with the various version tags
    puts "Tagging #{image_id} with version tags:"
    version_tags.each do |tag|
      puts "\tchef/#{image_name}:#{tag}"
      %x{docker tag #{image_id} chef/#{image_name}:#{tag}}
    end

    # Cleanup
    clear_tempdir
  end

  desc 'release specified image to Docker Hub'
  task :release, :image_name do |t, args|
    sh "docker push chef/#{args[:image_name]}"
  end

  desc 'build all the available images'
  task :build_all, :version do |t, args|
    version = args[:version] || @latest_version
    Dir.entries('Dockerfiles').select do |platform|
      unless platform == '.' || platform == '..'
        Rake::Task['docker:build'].invoke(platform, version)
        Rake::Task['docker:build'].reenable
      end
    end
  end

  desc 'release all the available images'
  task :release_all do |t, args|
    Dir.entries('Dockerfiles').select do |platform|
      unless platform == '.' || platform == '..'
        Rake::Task['docker:release'].invoke(platform)
        Rake::Task['docker:release'].reenable
      end
    end
  end
end


#
# Build a custom Dockerfile for the version of Chef you wish to install
#
# @example
#   render_dockerfile('ubuntu:12.04', '11.12.8')
#
# @param [String] image_name
#   The name of the image you wish to use. Will accept tags.
#
# @param [String] version
#   The version of chef-client which you wish to install.
#
def render_dockerfile(image_name, version)
  dockerfile = File.join(
    File.expand_path(File.dirname(__FILE__)),
    "Dockerfiles",
    image_name,
    "Dockerfile.erb"
  )
  template = ERB.new(File.read(dockerfile))
  result = template.result(binding)
  dest = "#{tempdir}/Dockerfile"

  File.open(dest, 'w') do |file|
    file.write(result)
  end
end

# Create a temp directory if one doesn't exist
def tempdir
  @tmpdir ||= Dir.mktmpdir("dockerfile")
  File.realpath(@tmpdir)
end

# Clear the temp directory
def clear_tempdir
  FileUtils.rm_rf(tempdir)
  @tmpdir = nil
end
