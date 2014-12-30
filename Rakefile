require 'rake'
require 'teamcity'
require 'hashie'
require 'yaml'

TeamCity.configure do |config|
  config.endpoint = 'http://ci.tsr.me/guestAuth/app/rest'
end

desc 'Exports teamcity builds'
task :teamcity do
  results = Hashie::Mash.new

  builds = TeamCity.builds(buildType: 'AppliedEnergistics_ReleaseBuild', status: 'SUCCESS')
  builds.each do |b|
    teamcity_build = TeamCity.build(id: b.id)
    teamcity_artifacts = TeamCity.build_artifacts(b.id)


    teamcity_properties = teamcity_build.properties.property
    revision = teamcity_properties.find { |p| p.name == 'DefaultBranch' }.value
    channel = teamcity_properties.find { |p| p.name == 'buildType' }.value
    build = teamcity_properties.find { |p| p.name == 'buildNumber' }.value
    #puts "#{revision}-#{channel}-#{build}"

    artifacts = []
    teamcity_artifacts.each do |a|
      artifacts << a.name
    end

    result = Hashie::Mash.new({revision => {:channels => {channel => {:builds => {build => {:teamcityID => b.id, :artifacts => artifacts} }}}}})
    results.deep_merge!(result)
  end

  File.open('_data/downloads.yaml', 'w') { |file| file.write(YAML.dump(results.to_hash)) }
end