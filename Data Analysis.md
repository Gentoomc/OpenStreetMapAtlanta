
## Data Wrangling-Atlanta from OpenStreetMap 


    import xml.etree.cElementTree as ET
    import pprint
    import re
    import os
    os.chdir('/Users/miricho/Desktop/Udacity/P3_Data Wrangling/data')
    filename = 'atlanta_georgia.osm'


    tree = ET.parse(filename)
    root = tree.getroot()

####Understanding Data & Data Auditing


    len(root)




    12056546




    #This function counts each tag and save as a dictionary
    def count_tags(filename):
        tree = ET.parse(filename)
        root = tree.getroot()
    
        dict_tag = {}
        tag_list = []
        
        tag_list.append(str(root).split("'")[1])
        for tag in root:
            tag_list.append(str(tag).split("'")[1])
            for tag_1 in tag:
                tag_list.append(str(tag_1).split("'")[1])
                for tag_2 in tag_1:
                    tag_list.append(str(tag_2).split("'")[1])
        
        key_list = list(set(tag_list))
        for key in key_list:
            dict_tag[key] = tag_list.count(key)
        print dict_tag
        return dict_tag


    count_tags(filename)

    {'node': 11287705, 'nd': 12976083, 'bounds': 1, 'member': 35466, 'tag': 6105957, 'osm': 1, 'way': 764672, 'relation': 4168}





    {'bounds': 1,
     'member': 35466,
     'nd': 12976083,
     'node': 11287705,
     'osm': 1,
     'relation': 4168,
     'tag': 6105957,
     'way': 764672}




    #show a sample of each parent and child tag and attrib to see what the file looks like
    for neighbor in root.findall('node')[:1]:
        print neighbor.tag, neighbor.attrib
        for i in neighbor.findall('tag'):
            print i.tag, i.attrib
            
    for neighbor in root.findall('relation')[:1]:
        print neighbor.tag, neighbor.attrib
        for i in neighbor.findall('member'):
            print i.tag, i.attrib
        for s in neighbor.findall('tag'):
            print s.tag, s.attrib
    
    for neighbor in root.findall('way')[:1]:
        print neighbor.tag, neighbor.attrib
        for i in neighbor.findall('nd'):
            print i.tag, i.attrib
        for s in neighbor.findall('tag'):
            print s.tag, s.attrib

    node {'changeset': '14903606', 'uid': '1081376', 'timestamp': '2013-02-03T22:37:10Z', 'lon': '-84.262916', 'version': '3', 'user': 'greenv505', 'lat': '34.0852695', 'id': '1481302'}
    relation {'changeset': '7434248', 'uid': '396090', 'timestamp': '2011-03-01T23:29:56Z', 'version': '14', 'user': 'upstream', 'id': '66265'}
    member {'ref': '29732998', 'role': 'outer', 'type': 'way'}
    member {'ref': '30044699', 'role': 'inner', 'type': 'way'}
    member {'ref': '34460006', 'role': 'inner', 'type': 'way'}
    member {'ref': '101220876', 'role': 'outer', 'type': 'way'}
    member {'ref': '101287212', 'role': 'inner', 'type': 'way'}
    member {'ref': '101295167', 'role': 'outer', 'type': 'way'}
    member {'ref': '101295164', 'role': 'outer', 'type': 'way'}
    member {'ref': '101295160', 'role': 'outer', 'type': 'way'}
    member {'ref': '101295161', 'role': 'outer', 'type': 'way'}
    member {'ref': '101295157', 'role': 'outer', 'type': 'way'}
    member {'ref': '101295158', 'role': 'outer', 'type': 'way'}
    member {'ref': '101640460', 'role': 'outer', 'type': 'way'}
    member {'ref': '101640454', 'role': 'outer', 'type': 'way'}
    member {'ref': '101640455', 'role': 'outer', 'type': 'way'}
    member {'ref': '101665629', 'role': 'outer', 'type': 'way'}
    member {'ref': '101665615', 'role': 'outer', 'type': 'way'}
    member {'ref': '101665623', 'role': 'outer', 'type': 'way'}
    member {'ref': '101668289', 'role': 'outer', 'type': 'way'}
    member {'ref': '101737821', 'role': 'outer', 'type': 'way'}
    member {'ref': '101737808', 'role': 'outer', 'type': 'way'}
    member {'ref': '101742111', 'role': 'outer', 'type': 'way'}
    member {'ref': '102238996', 'role': 'outer', 'type': 'way'}
    member {'ref': '102238990', 'role': 'outer', 'type': 'way'}
    member {'ref': '102238991', 'role': 'outer', 'type': 'way'}
    member {'ref': '102238993', 'role': 'outer', 'type': 'way'}
    member {'ref': '102278374', 'role': 'outer', 'type': 'way'}
    member {'ref': '102357553', 'role': 'outer', 'type': 'way'}
    member {'ref': '102357554', 'role': 'outer', 'type': 'way'}
    tag {'k': 'name', 'v': 'Stone Mountain Lake'}
    tag {'k': 'type', 'v': 'multipolygon'}
    tag {'k': 'natural', 'v': 'water'}
    way {'changeset': '14477992', 'uid': '451693', 'timestamp': '2012-12-31T14:20:15Z', 'version': '2', 'user': 'bot-mode', 'id': '6252596'}
    nd {'ref': '52374104'}
    nd {'ref': '52374106'}
    nd {'ref': '52374108'}
    nd {'ref': '52374110'}
    tag {'k': 'name', 'v': 'North 8th Drive'}
    tag {'k': 'highway', 'v': 'residential'}
    tag {'k': 'tiger:cfcc', 'v': 'A41'}
    tag {'k': 'tiger:county', 'v': 'Chambers, AL'}
    tag {'k': 'tiger:reviewed', 'v': 'no'}
    tag {'k': 'tiger:zip_left', 'v': '36863'}
    tag {'k': 'tiger:name_base', 'v': '8th'}
    tag {'k': 'tiger:name_type', 'v': 'Dr'}
    tag {'k': 'tiger:zip_right', 'v': '36863'}
    tag {'k': 'tiger:name_direction_prefix', 'v': 'N'}



    #return a set of each level
    first_level = []
    second_level = []
    third_level = []
    for i in root:
        first_level.append(i.tag)
        for j in i:
            second_level.append(j.tag)
            for k in j:
                second_level.append(k.tag)
                
    first_level = set(first_level)
    second_level = set(second_level)
    third_level = set(third_level)
    
    print "first_level: ", first_level
    print "second_level: ", second_level
    print "third_level: ", third_level

    first_level:  set(['node', 'relation', 'bounds', 'way'])
    second_level:  set(['member', 'tag', 'nd'])
    third_level:  set([])



    #show child level of each first level parent and return a dictionary of each pair
    parent_child_dict = {}
    for level in first_level:
        node_child = []
        for parent in root.findall(level):
            for child in parent:
                node_child.append(child.tag)
        node_child = set(node_child)
        print "{0}: ".format(level), node_child
        parent_child_dict[level] = node_child
    #print parent_child_dict

    node:  set(['tag'])
    relation:  set(['member', 'tag'])
    bounds:  set([])
    way:  set(['tag', 'nd'])
    {'node': set(['tag']), 'relation': set(['member', 'tag']), 'bounds': set([]), 'way': set(['tag', 'nd'])}


##### # of unique k values for each parent


    #show all unique keys and k values for each parent
    def listtostring(tag_keys):
        tag_list = []
        for i in tag_keys:
            for s in i:
                tag_list.append(s)
        tag_list = set(tag_list)
        return tag_list
    
    parent_keys, member1_keys, member2_keys, k_attrib = [], [], [], []
    for neighbor in root.findall('way'):
        parent_keys.append(neighbor.attrib.keys())
        for i in neighbor.findall('nd'):
            member1_keys.append(i.attrib.keys()) 
        for i in neighbor.findall('tag'):
            member2_keys.append(i.attrib.keys()) 
            k_attrib.append(i.attrib.get('k'))
    k_attrib = set(k_attrib)
    
    print "parent_keys: ", listtostring(parent_keys)
    print "member1_keys: ", listtostring(member1_keys)
    print "member2_keys: ", listtostring(member2_keys)
    print "k_attrib len: ", len(k_attrib)
    print "k_attrib: ", k_attrib

    parent_keys:  set(['changeset', 'uid', 'timestamp', 'version', 'user', 'id'])
    member1_keys:  set(['ref'])
    member2_keys:  set(['k', 'v'])
    k_attrib len:  621
    k_attrib:  set(['GADOT_HWY:source', 'tiger:source', 'maxspeed', 'motorhome', 'class:bicycle', 'Common Area', 'destination:forward', 'turn:lanes:forward', 'disused:LandPro08:DE3', 'is_in', 'source:HFCS', 'fenced', 'contact:youtube', 'created_by', 'to', 'blaze', 'gatech:BLDG_NAME', 'fax', 'attribution', 'local:id', 'icao', 'tiger:county', 'name:uk', 'motor_vehicle', 'faa', 'addr:street', 'source:name', 'contact:facebook', 'OWNER_NAME', 'minspeed', 'level', 'gatech:ATL_BLDG_I', 'sidewalk', 'disused', 'recycling:paper', 'snowmobile', 'center_turn_lane', 'FayettevilleGIS:BLDG_HEIGH', 'gnis:state_id', 'bicycle', 'gnis:county_name', 'crop', 'lanes:reversible', 'contact:google_plus', 'area:highway', 'addr:street:source', 'access:bus', 'phone_1', 'addr', 'leaf_type', 'access', 'note:opening_hours', 'source:bridge', 'toll', 'source:website', 'FayettevilleGIS:ST_NAM', 'GADOT_HWY:reviewed', 'building:height', 'tiger:direction', 'destination:ref:to', 'gatech:OWNERSHIP', 'water', 'tracks', 'tower:type', 'baseball', 'opening_hours:visitors', 'contact:twitter', 'source:maxspeed_1', 'tiger:zip', 'hoops', 'passengers', 'parking:lane:left:parallel', 'FayettevilleGIS:', 'fuel:e10', 'source:lanes', 'LandPro08:LC_NAME', 'military', 'NHD:GNIS_Name', 'fcgis:STORIES', 'hiking', 'tiger:zip_right', 'survey:date', 'turn:lanes', 'amenity', 'private', 'county', 'addr:state', 'nist:fips_code', 'nist:state_fips', 'fee', 'FIXME:ref', 'tiger:name_type_3', 'tiger:name_type_2', 'tiger:name_type_1', 'destination', 'LandPro08:LU', 'USGS-LULC:LEVEL_II', 'townhall:type', 'building:parts', 'vehicle', 'LandPro08:LC', 'type', 'start_date', 'addr:unit', 'drinkable', 'end_date', 'motorroad', 'capacity:women', 'phone', 'rabbit', 'source:cycleway', 'contact:pinterest', 'segregated', 'traffic_calming', 'tunnel', 'car', 'google_plus', 'source:note', 'tiger:NAME', 'noref', 'theatre:genre', 'bridge:type', 'history', 'inscription', 'note:highway', 'generator:source', 'building:min_level', 'rcn_ref', 'source:hgv:national_network', 'information', 'recycling:cans', 'lanes:backward', 'divided', 'hov', 'parking:lane:left:capacity', 'NHS', 'nat_ref', 'overtaking', 'old_railway_operator', 'disused:shop', 'buildtype', 'FIXME', 'iata', 'description', 'memorial', 'footpath', 'short', 'natural', 'lcn_ref', 'roundtrip', 'gatech:BLDG_TYPE', 'addr:interpolation', 'gatech:UNDERGROUND', 'takeaway', 'note:foot', 'FayettevilleGIS:PARCEL_KEY', 'fcgis:BLDG_FORM', 'office', 'building:part', 'addr:housenumber_1', 'note:lanes', 'industrial', 'postal_code', 'motorcycle', 'pitch', 'agricultural', 'addr:housenumber', 'LandPro08:LCLU', 'alt_name_1', 'drive_in', 'tiger:name_type', 'SUBDIVIS_1', 'capacity:disabled', 'covered', 'image', 'old_ref', 'junction', 'animal', 'school', 'cutting', 'capacity:parent', 'foot', 'tourism', 'smoothness', 'gnis:edited', 'hov:lanes', 'fixme', 'USGS-LULC:CLASS', 'addr:city', 'designation', 'tiger:zip_left', 'fcgis:GFSF', 'embankment', 'crossing', 'name_2', 'name_3', 'name_1', 'name_4', 'house', 'owner', 'source:length', 'frequency', 'loc_name', 'mtb:scale:uphill', 'ref:nrhp', 'subway', 'network', 'social_facility_for', 'USFS:way_id', 'ref', 'highway', 'source:destination', 'isced:level', 'barrier', 'fence_type', 'maxweight', 'tiger:reviewed', 'electrified', 'roof:shape', 'maxspeed:variable', 'turn:lanes:backward', 'FayettevilleGIS:Shape_Leng', 'hgv:minaxles', 'noexit', 'service', 'addr:inclusion', 'route', 'atm', 'source:geometry', 'shelter_type', 'length', 'place', 'second_hand', 'parking:lane:both:parallel', 'clothes', 'tiger:PLACENS', 'abandoned:highway', 'tennis', 'sac_scale', 'USFS:multi_rout', 'parking:lane:both', 'opening_hours:pharmacy', 'parking:lane:right:parallel', 'rail', 'phone:showtimes', 'city', 'horse', 'tiger:upload_uuid', 'aerialway', 'width', 'name_', 'note:access', 'addr:housename', 'border_type', 'FayettevilleGIS:ST_NUM', 'motorcar', 'park_ride', 'paved', 'tiger:NAMELSAD', 'OBJECTID', 'STREET1', 'is_in:state', 'README', 'roof:height', 'opening_hours:cafe', 'bridge:source', 'distance', 'tiger:name_direction_suffix', 'aeroway', 'wetland', 'note:name', 'landuse', 'tracktype', 'wheelchair', 'bridge', 'source:highway', 'outdoor_seating', 'building:levels:aboveground', 'tiger:PCICBSA', 'mtb:scale:imba', 'noname', 'generator:method', 'railway_milepost', 'fcgis:GEO_OID', 'source:construction', 'tiger:LSAD', 'hgv', 'gatech:OBJECTID_2', 'lit', 'FayettevilleGIS:BLDG_WALL', 'FayettevilleGIS:FID_', 'source:addr', 'animal_shelter:release', 'min_levels', 'oneway_1', 'aerodrome', 'note:addr', 'rooms', 'url', 'gdot:grip', 'tiger:MTFCC', 'tiger:name_direction_suffix_4', 'tiger:name_direction_suffix_2', 'tiger:name_direction_suffix_3', 'tiger:name_direction_suffix_1', 'opening_hours:shop', 'is_in:iso_3166_2', 'quantity', 'shop', 'golf', 'healthcare:speciality', 'note:sidewalk', 'indoor', 'social_facility', 'gauge', 'LandPro08:DE5', 'trail_visibility', 'LandPro08:DE3', 'USGS-LULC:CNTYNAME', 'tiger:zip_left_4', 'tiger:zip_left_3', 'tiger:zip_left_2', 'tiger:zip_left_1', 'gnis:feature_type', 'access:hgv', 'is_in:city', 'NHD:ComID', 'wood', 'addr:suite', 'building:roof', 'service_times', 'FayettevilleGIS:address_hi', 'leisure', 'tourist_bus', 'gnis:County', 'tiger:zip_right_10', 'name:en', 'contact:fax', 'FayettevilleGIS:YR_BUILT', 'tiger:cfcc', 'addr:postcode', 'USGS-LULC:STATECTY', 'LandPro08:LU_NAME', 'email', 'internet_access:fee', 'public_transport', 'gatech:SUBTYPE', 'tiger:zip_right_5', 'tiger:zip_right_4', 'int_name', 'NHD:ReachCode', 'tiger:STATEFP', 'source:ref', 'driveway', 'parking', 'name:ru', 'sport', 'capacity', 'fuel:diesel', 'wikipedia', 'weapons', 'change:lanes:forward', 'generator:output:electricity', 'closed', 'boundary', 'lanes:forward', 'city_served', 'screen', 'Abundance LLC Wealth Advisors', 'denomination', 'key', 'building:use', 'substation', 'hours', 'tiger:PLACEFP', 'fcgis:YR_BUILT', 'ramp', 'restroom', 'cycleway:right', 'local_name', 'DeKalb:id', 'railway', 'animal_species', 'maxspeed:backward', 'drive_through', 'parking:lane:left', 'fcgis:STRUCT_USE', 'height', 'long_name', 'name:cycleway', 'name:de', 'boat', 'tiger:name_direction_prefix_4', 'tiger:name_direction_prefix_1', 'tiger:name_direction_prefix_3', 'tiger:name_direction_prefix_2', 'gatech:ATL_BLDG_', 'NHD:FCode', 'service_times:es', 'parking:lane:both:diagonal', 'basin', 'gatech:TYPE', 'FayettevilleGIS:address_lo', 'website', 'note:old_railway_operator', 'abandoned:amenity', 'lanes', 'note:maxspeed', 'tiger:linearid', 'microbrewery', 'aerodrome:type', 'source:date', 'tiger:name_type_4', 'craft', 'maxheight', 'gnis:import_uuid', 'NHD:way_id', 'smoking', 'legal:video', 'NHD:FDate', 'abandoned', 'n', 'parking_space', 'building:levels:underground', 'FayettevilleGIS:Length', 'demolished:building', 'TYPE', 'gatech:BLDG_NUM', 'railway:traffic_mode', 'layer', 'handrail', 'addr:building', 'toilets:disposal', 'animal_shelter:adoption', 'gnis:county_id', 'surface', 'social_facility:for', 'waterway', 'id', 'zip_right', 'cuisine', 'FayettevilleGIS:BLDG_TYPE', 'unsigned_ref', 'nature_trail', 'emergency', 'GADOT_HWY:source_1', 'USGS-LULC:LEVEL_I', 'gatech:PRKG_NUM', 'access:psv', 'surveillance', 'collection_times', 'firearms', 'vegetable', 'gatech:GlobalID', 'source:sidewalk', 'source:maxspeed', 'short_name_1', 'access:motor_vehicle:emergency', 'levels', 'ownership', 'NHD:GNIS_ID', 'HFCS', 'culvert', 'name:dam', 'parking:lane:right', 'undefined', 'website_1', 'intermittent', 'animal_boarding', 'building:levels', 'oneway', 'landmark', 'addr:country', 'taxi', 'proposed', 'FayettevilleGIS:BLDG_SIZE', 'NHD:FTYPE', 'tiger:zip_right_1', 'tiger:mtfcc', 'tiger:zip_right_3', 'tiger:zip_right_2', 'tiger:tlid', 'voltage', 'tiger:zip_right_7', 'tiger:zip_right_6', 'guest_house', 'note', 'FIXME:hgv', 'tiger:PLCIDFP', 'parking:lane:left:diagonal', 'ele', 'source', 'note:railway', 'NHD:Elevation', 'location', 'operator_1', 'usage', 'gnis:feature_id', 'ski', 'zip_left', 'short_name', 'payment:bitcoin', 'fuel:biodiesel', 'hgv:national_network', 'Pkwy', 'tiger:separated', 'historic', 'gatech:UNDERGROUN', 'destination:lanes:forward', 'lcn', 'psv', 'name', 'animal_shelter', 'tiger:name_base', 'works', 'alt_name', 'gatech:LASTUPDATE', 'guage', 'destination:backward', 'authority', 'NHD:RESOLUTION', 'destination:ref', 'USFS:systemname', 'note:addr:housenumber', 'man_made', 'religion', 'centre_turn_lane', 'destination:lanes', 'tiger:CLASSFP', 'note:width', 'parking:lanes:both', 'cycleway:left', 'power', 'maxspeed:advised', 'FayettevilleGIS:Id', 'gatech:PERIMETER', 'offset', 'is_in:state_code', 'goods', 'incline', 'footway', 'compressed_air', 'supervised', 'tiger:name_direction_prefix', 'payment:litecoin', 'tiger:name_suffix', 'tomb', 'internet_access', 'name_type', 'mofa', 'step_count', 'health_specialty', 'contact:website', 'mtb:scale', 'moped', 'operator', 'gatech:Reference_', 'tiger:FUNCSTAT', 'tiger:CPI', 'FayettevilleGIS:Text_', 'area', 'opening_hours', 'contact:phone', 'damaged', 'tiger:name_base_1', 'tiger:name_base_2', 'tiger:name_base_3', 'tiger:name_base_4', 'tiger:name_base_5', 'heritage', 'tiger:PCINECTA', 'old_name_1', 'admin_level', 'bus', 'brand', 'ford', 'delivery', 'construction', 'golf_cart', 'courts', 'old_name', 'line', 'pavilion', 'source:maxspeed:advised', 'oneway:bicycle', 'NHD:FType', 'highway:source', 'recycling:clothes', 'County', 'gnis:reviewed', 'dispensing', 'drinking_water', 'fut_ref', 'destination:lanes:backward', 'reg_name', 'official_name', 'source:opening_hours', 'shooting', 'cables', 'is_in:country', 'cycleway', 'gatech:SHAPE_LENG', 'branch', 'gatech:OBJECTID', 'note_1', 'is_in:country_code', 'min_height', 'building_1', 'maxstay', 'addr:county', 'gnis:created', 'building', 'traffic_signals:direction', 'phone:2', 'wifi', 'closest_town', 'Street', 'protect_class', 'passing_places', 'maxspeed:forward', 'heritage:operator'])



    k_attrib = []
    for neighbor in root.findall('node'):
        for i in neighbor.findall('tag'):
            k_attrib.append(i.attrib.get('k'))
    k_attrib = set(k_attrib)
    print "k_attrib len: ", len(k_attrib)
    print k_attrib

    k_attrib len:  424
    set(['maxspeed', 'is_in', 'max_age', 'created_by', 'aquatic center', 'blaze', 'crossing:barrier', 'gatech:BLDG_NAME', 'fax', 'attribution', 'name:ur', 'icao', 'name:uk', 'motor_vehicle', 'faa', 'addr:street', 'alt_name_2', 'source:name', 'school', 'level', 'name:zh', 'highway', 'is_in:continent', 'sidewalk', 'clothes', 'gnis:state_id', 'bicycle', 'gnis:county_name', 'contact:google_plus', 'design', 'hygrometer', 'phone_1', 'exit_to', 'access', 'note:opening_hours', 'personal_trainer', 'barometer', 'voltage-high', 'men', 'census:population', 'name:bo', 'ethanol', 'contact:twitter', 'name:be', 'name:bg', 'microbrewery', 'addr:suit', 'fuel:e10', 'STREETDIR', 'military', 'massage', 'whitewater', 'gnis:County_num', 'hiking', 'surveillance:type', 'survey:date', 'amenity', 'alt_name', 'apc', 'name:pl', 'fee', 'FIXME:ref', 'diet:vegetarian', 'music', 'type', 'start_date', 'addr:unit', 'tower:construction', 'visibility', 'gnis:county_id', 'phone', 'train', 'Time', 'traffic_calming', 'google_plus', 'source:note', 'service:bicycle:chain_tool', 'noref', 'theatre:genre', 'male', 'fire_hydrant:position', 'history', 'inscription', 'name:mr', 'electronics_repair', 'name:my', 'hov:minimum', 'name:ml', 'clothing', 'name:mk', 'name:fa', 'aeroway:field_ele', 'swing_gate:type', 'animal', 'exit_to:left', 'FIXME', 'iata', 'description', 'information', 'aeroway:ele', 'date', 'natural', 'crossing:light', 'wheelchair', 'outdoor_seating', 'linen', 'deposit', 'takeaway', 'population:date', 'office', 'building:part', 'radar', 'name:th', 'postal_code', 'motorcycle', 'name:ta', 'exit_to:right', 'population:source', 'addr:housenumber', 'cash_in', 'attraction', 'gatech:ATL_BLDG_', 'collection_times', 'camera:mount', 'capacity:disabled', 'covered', 'old_ref', 'junction', 'food', 'material', 'contact:facebook', 'milestone', 'foot', 'map_type', 'building:level_min', 'rental', 'gatech:TYPE', 'gnis:edited', 'payment:bitcoin', 'fixme', 'name', 'designation', 'addr:state', 'crossing', 'kerb', 'street_address', 'name_2', 'name_1', 'name:ar', 'books', 'source:exit_to', 'Type', 'alcohol', 'diesel', 'pet:grooming', 'ref', 'gatech:ATL_BLDG_I', 'subway', 'source:maxspeed', 'isced:level', 'barrier', 'import_uuid', 'name:sr', 'noexit', 'organic', 'route', 'atm', 'shelter_type', 'takeout', 'unisex', 'place', 'toilets:wheelchair', 'note', 'opening_hours:pharmacy', 'owner', 'horse', 'aerialway', 'addr:housename', 'motorcar', 'id_numbers', 'park_ride', 'OBJECTID', 'name:he', 'service:bicycle:pump', 'artist_name', 'is_in:state', 'communication:mobile_phone', 'name:hy', 'name:ht', 'crossing:bell', 'population', 'link:google_plus', 'socket:type1', 'aeroway', 'note:name', 'landuse', 'healthcare', 'sport', 'building:levels', 'insciption:url', 'gatech:OBJECTID_2', 'zoo', 'lit', 'thermometer', 'towards', 'addr:full', 'ref:left', 'payment', 'state_capital', 'payment:coins', 'url', 'sale', 'addr:country', 'tactile_paving', 'amenity2', 'quantity', 'shop', 'name:ka', 'healthcare:speciality', 'gnis:ST_num', 'name:kn', 'automated', 'social_facility', 'name:kw', 'gnis:id', 'gnis:feature_type', 'is_in:city', 'fuel', 'inscription_1', 'addr:suite', 'name:lt', 'service_times', 'stop', 'leisure', 'name:la', 'gnis:County', 'name:el', 'name:eo', 'name:en', 'addr:postcode', 'internet_access:fee', 'public_transport', 'gatech:SUBTYPE', 'raceway', 'name:es', 'int_name', 'map_size', 'note:access', 'source:pkey', 'Location', 'parking', 'name:ru', 'traffic_signals:sound', 'capacity', 'fuel:diesel', 'contact:email', 'wikipedia', 'closed', 'ele', 'name:te', 'email', 'stop:direction', 'music_genre', 'home_visit', 'denomination', 'key', 'building:use', 'condo', 'tower:type', 'board_type', 'country', 'theatre:type', 'faces', 'STREETSUB', 'railway', 'comment', 'drive_through', 'height', 'long_name', 'name:os', 'bench', 'surveillance:zone', 'maxspeed:advised', 'drive_in', 'ref:right', 'bicycle_parking', 'website', 'direction', 'controlled', 'inscription:url', 'level_crossing', 'historical:name', 'gnis:import_uuid', 'smoking', 'legal:video', 'import:FEAT_TYPE', 'name:yi', 'note:ref', 'layer', 'backrest', 'addr:building', 'toilets:disposal', 'gatech:UNDERGROUN', 'surface', 'social_facility:for', 'waterway', 'cuisine', 'memorial', 'short_name', 'gatech:PRKG_NUM', 'surveillance', 'parts', 'firearms', 'gatech:GlobalID', 'nearest_intersection', 'drink', 'nearest_building', 'marker_date', 'ownership', 'culvert', 'gnis:ST_alpha', 'website_1', 'animal_boarding', 'colour', 'auto_parts_store', 'elev', 'landmark', 'Id', 'amperage', 'traffic_signal', 'voltage', 'seats', 'name:ce', 'toilets', 'tourism', 'geological', 'guidepost', 'source', 'location', 'operator_1', 'historical:building', 'gnis:feature_id', 'furniture', 'emergency', 'Bike_Route', 'fuel:biodiesel', 'historic', 'addr:city', 'vending', 'internet_access', 'gatech:LASTUPDATE', 'authority', 'gymnastics', 'local_ref', 'wpt_description', 'man_made', 'religion', 'artwork_type', 'name:ja', 'power', 'gatech:PERIMETER', 'nudism', 'is_in:state_code', 'footway', 'monitoring_station', 'gnis:Class', 'supervised', 'addr:usenumber', 'payment:litecoin', 'repair', 'health_specialty', 'contact:website', 'name:ko', 'operator', 'gatech:Reference_', 'sanitary_dump_station:round_drain', 'area', 'button_operated', 'opening_hours', 'support', 'contact:phone', 'bus', 'brand', 'ford', 'delivery', 'dance:teaching', 'golf_cart', 'old_name', 'highway:source', 'County', 'gnis:reviewed', 'dispensing', 'old_name:ja', 'mtb', 'display', 'car_wash', 'entrance', 'dog', 'source:opening_hours', 'pricerange', 'lanes', 'shooting', 'recycling_type', 'is_in:country', 'traffic_sign', 'gatech:SHAPE_LENG', 'self_service', 'gatech:OBJECTID', 'fire_hydrant:type', 'shelter', 'Route', 'is_in:country_code', 'min_age', 'addr:county', 'gnis:created', 'building', 'traffic_signals:direction', 'wifi', 'closest_town', 'traffic_signals', 'fuel:octane_87', 'craft', 'name:gu', 'time'])



    parent_keys = []
    for neighbor in root.findall('node'):
        parent_keys.append(neighbor.attrib.keys())
    print "parent_keys: ", listtostring(parent_keys)

    parent_keys:  set(['changeset', 'uid', 'timestamp', 'lon', 'version', 'user', 'lat', 'id'])



    #data auditing - check for problem characters
    lower = re.compile(r'^([a-z]|_)*$')
    lower_colon = re.compile(r'^([a-z]|_)*:([a-z]|_)*$')
    problemchars = re.compile(r'[=\+/&<>;\'"\?%#$@\,\. \t\r\n]')
    
    for level in first_level:
        #for each parent level tag, return a dict of problem keys
        keys = {"lower": 0, "lower_colon": 0, "problemchars": 0, "other": 0}
        for parent in root.findall(level):
            for element in parent.findall('tag'):
                for k_val in element.get("k"):
                    if bool(lower.search(k_val)):
                        keys["lower"] += 1
                    elif bool(lower_colon.search(k_val)): 
                        keys["lower_colon"] += 1
                    elif bool(problemchars.search(k_val)):
                        keys["problemchars"] += 1
                    else:
                        keys["other"] += 1
        print level, keys

    node {'problemchars': 1, 'lower': 16560232, 'other': 33387, 'lower_colon': 523655}
    relation {'problemchars': 0, 'lower': 120699, 'other': 27143, 'lower_colon': 7228}
    bounds {'problemchars': 0, 'lower': 0, 'other': 0, 'lower_colon': 0}
    way {'problemchars': 4, 'lower': 36781613, 'other': 6276002, 'lower_colon': 2816654}



    #main code
    #1) shape_element: Go through each element and put them in a format
    #2) update_name: uses mapping dictionary to re-format the street types
    #3) process_map: save the output as a josn file
    
    import xml.etree.cElementTree as ET
    import pprint
    import re
    import codecs
    import json
    import os
    os.chdir('/Users/miricho/Desktop/Udacity/P3_Data Wrangling/data')
    filename = 'atlanta_georgia.osm'
    
    lower = re.compile(r'^([a-z]|_)*$')
    lower_colon = re.compile(r'^([a-z]|_)*:([a-z]|_)*$')
    problemchars = re.compile(r'[=\+/&<>;\'"\?%#$@\,\. \t\r\n]')
    
    
    #improving street names
    CREATED = [ "version", "changeset", "timestamp", "user", "uid"]
    street_type_re = re.compile(r'\b\S+\.?$', re.IGNORECASE)
    expected = ["Street", "Avenue", "Boulevard", "Drive", "Court", "Place", "Square", "Lane", "Road", 
                "Trail", "Parkway", "Commons"]
    mapping = { "st": "street",
                "st.": "street",
                "rd": "road",
                "rd.":"road",
                "ave" : "avenue",
                "ave." : "avenue",
                "cir" : "circle",
                "blvd" : "boulevard",
                "ct" : "court",
                "sq" : "square",
                "pkwy" : "parkway",
                "ln" : "lane",
                "trl" : "trail",
                "dr" : "drive",
                }
    
    def update_name(name, mapping):
        lname = name.lower()
        for i in lname.split(" "):
            if i in mapping.keys():
                lname = lname.replace(i,mapping[i])
        return lname
        
    
    def shape_element(element):
        node = {}
        pos = ["lat", "lon"]
        
        if element.tag == "node" or element.tag == "way" :
            node["created"] = {}
            node["address"] = {}
            node["pos"] = []
            node["node_refs"] = []
            node["type"] = element.tag
            for key in element.attrib.keys():
                if key in CREATED:
                    node["created"][key] = element.attrib[key]
                elif key in pos:
                    if key == "lat":
                        if len(node["pos"]) == 0:
                            node["pos"].append(float(element.attrib[key]))
                        else:
                            node["pos"][0] = float(element.attrib[key])
                    else:
                        if len(node["pos"]) == 0:
                            node["pos"].append("")
                        node["pos"].append(float(element.attrib[key]))
                        #node["pos"].append(float(element.attrib[key]))
                else:
                    node[key] = element.attrib[key]
    
            # for all tags with k value within one element, create a dictionary for repeated k values before the first colon (if exists)
            k_values = [i.attrib.get('k') for i in element if i.tag == "tag"]
            split_k_value = [el.split(":")[0] for el in k_values if el.count(":") >= 1]
            repeat_k_value = {}
            
    
            for i in set(split_k_value):
                repeat_k_value[i] = split_k_value.count(i)
                node[i] = {}
    
            # if k and v are available, add them to dict
            for i in element:
                if (i.tag == "tag") and (bool(problemchars.search(i.attrib.get('k')))==False):
    
                    # if i.attrib['k'] == "addr:street":
                    #     i.attrib['v'] = update_name(i.attrib['v'], mapping)
    
                    before_colon = i.attrib.get('k').split(":")[0]
                    after_colon = ":".join(i.attrib.get('k').split(":")[1:]) 
    
                    if before_colon in repeat_k_value.keys():
                        if i.attrib['k'] == "addr:street":
                            node[before_colon][after_colon] = update_name(i.attrib['v'], mapping)
                        else:
                            node[before_colon][after_colon] = i.attrib.get('v')
    
                    else:
                        node[i.attrib.get('k')] = i.attrib.get('v')
                elif (i.tag == "nd"):
                    node["node_refs"].append(i.attrib.get('ref'))    
                            
            #delete empty items
            items_delete = []
            for i in node:
                if not node[i]:
                    items_delete.append(i)
            for i in items_delete:
                del node[i]
    
            return node
        else:
            return None
    
    
    def process_map(file_in, pretty = False):
        file_out = "{0}.json".format(file_in)
        data = []
        with codecs.open(file_out, "w") as fo:
            for _, element in ET.iterparse(file_in):
                el = shape_element(element)
                if el:
                    data.append(el)
                    if pretty:
                        fo.write(json.dumps(el, indent=2)+"\n")
                    else:
                        fo.write(json.dumps(el) + "\n")
        return data



    data = process_map(filename, True)


    len(data)


    key_list = []
    for dict in data:
        for i in dict.keys():
            key_list.append(i)
    key_list = set(key_list)
    print key_list
    print len(key_list)

    set(['shop', 'maxspeed', 'golf', 'office', 'wikipedia', 'social_facility', 'lcn', 'park_ride', 'industrial', 'postal_code', 'motorcycle', 'agricultural', 'cycleway', 'is_in', 'max_age', 'OWNER_NAME', 'NHD', 'proposed', 'abandoned', 'created_by', 'ele', 'source', 'drive_in', 'wood', 'location', 'operator_1', 'sac_scale', 'usage', 'covered', 'ski', 'fax', 'lit', 'attribution', 'emergency', 'Bike_Route', 'construction', 'material', 'stop', 'leisure', 'Pkwy', 'historic', 'motor_vehicle', 'foot', 'map_type', 'faa', 'tourism', 'smoothness', 'minspeed', 'county', 'fixme', 'artwork_type', 'vending', 'name', 'designation', 'level', 'guage', 'email', 'public_transport', 'aerialway', 'fuel', 'crossing', 'bench', 'sidewalk', 'kerb', 'gauge', 'internet_access', 'name_2', 'name_3', 'int_name', 'bicycle', 'map_size', 'authority', 'owner', 'frequency', 'design', 'Location', 'parking', 'loc_name', 'sport', 'Type', 'phone_1', 'exit_to', 'capacity', 'barrier', 'DeKalb', 'oneway', 'generator', 'FayettevilleGIS', 'man_made', 'legal', 'access', 'religion', 'centre_turn_lane', 'hov', 'toll', 'guidepost', 'boundary', 'gnis', 'ref', 'Route', 'highway', 'subway', 'node_refs', 'power', 'healthcare', 'import_uuid', 'fence_type', 'screen', 'electrified', 'denomination', 'water', 'mtb', 'tracks', 'incline', 'nudism', 'ethanol', 'condo', 'tower', 'substation', 'microbrewery', 'census', 'organic', 'segregated', 'footway', 'created', 'disused', 'route', 'atm', 'shelter_type', 'display', 'unisex', 'length', 'STREETDIR', 'place', 'faces', 'railway', 'clothes', 'name_type', 'Street', 'step_count', 'whitewater', 'drive_through', 'handrail', 'hiking', 'bridge', 'fcgis', 'craft', 'pos', 'height', 'leaf_type', 'long_name', 'name_1', 'alt_name', 'operator', 'wpt_description', 'passing_places', 'boat', 'horse', 'fee', 'gdot', 'service', 'area', 'button_operated', 'opening_hours', 'support', 'destination', 'layer', 'width', 'border_type', 'snowmobile', 'motorcar', 'basin', 'type', 'start_date', 'blaze', 'website', 'admin_level', 'entrance', 'diesel', 'lanes', 'OBJECTID', 'bus', 'brand', 'local', 'visibility', 'delivery', 'phone', 'gatech', 'noexit', 'link', 'official_name', 'old_name', 'smoking', 'old_name_1', 'population', 'distance', 'traffic_calming', 'survey', 'tunnel', 'car', 'google_plus', 'voltage', 'roof', 'bicycle_parking', 'roundtrip', 'parking_space', 'aeroway', 'noref', 'dispensing', 'addr', 'wetland', 'landuse', 'tracktype', 'colour', 'history', 'inscription', 'amenity', 'wifi', 'backrest', 'reg_name', 'direction', 'surface', 'zip_left', 'rcn_ref', 'railway_milepost', 'LandPro08', 'junction', 'waterway', 'cables', 'id', 'USGS-LULC', 'cuisine', 'unsigned_ref', 'short_name', 'turn', 'hgv', 'traffic_sign', 'surveillance', 'note', 'collection_times', 'demolished', 'NHS', 'nat_ref', 'supervised', 'old_railway_operator', 'zip_right', 'buildtype', 'FIXME', 'towards', 'description', 'theatre', 'information', 'shelter', 'nearest_intersection', 'time', 'short_name_1', 'min_age', 'min_height', 'nearest_building', 'marker_date', 'levels', 'old_ref', 'ownership', 'HFCS', 'payment', 'state_capital', 'building', 'tiger', 'fire_hydrant', 'natural', 'lcn_ref', 'USFS', 'url', 'wheelchair', 'outdoor_seating', 'closest_town', 'dog', 'traffic_signals', 'tactile_paving', 'contact', 'elev', 'takeaway', 'golf_cart', 'Id', 'quantity'])
    285


#####Validating the street address types


    exam = []
    for d in data:
        if 'addr' in d.keys():
            exam.append(d['addr'])


    new_st = []
    for ex in exam[:9000]:
        if 'street' in ex.keys():
            new_st.append(ex['street'])


    import random
    import pprint
    for i in range(3):
        rand = random.randint(0, len(data))
        pprint.pprint(data[rand])

    {'created': {'changeset': '1193241',
                 'timestamp': '2009-05-14T22:26:46Z',
                 'uid': '82286',
                 'user': 'Liber',
                 'version': '1'},
     'id': '399910616',
     'pos': [34.1504634, -83.6032192],
     'type': 'node'}
    {'created': {'changeset': '1183831',
                 'timestamp': '2009-05-14T07:06:28Z',
                 'uid': '82286',
                 'user': 'Liber',
                 'version': '1'},
     'id': '397796991',
     'pos': [34.048815, -83.377107],
     'type': 'node'}
    {'created': {'changeset': '30796297',
                 'timestamp': '2015-05-05T03:31:15Z',
                 'uid': '1946151',
                 'user': 'Jack the Ripper',
                 'version': '1'},
     'id': '3498364141',
     'pos': [33.8667286, -84.4950804],
     'type': 'node'}


###Creating a mongodb dabase from the json file


    import json
    !mongoimport --db udacity --collection atlanta --file atlanta_georgia.osm.json

    2015-08-19T09:21:07.925-0400	connected to: localhost
    2015-08-19T09:21:10.915-0400	[........................] udacity.atlanta	36.4 MB/3.2 GB (1.1%)
    2015-08-19T09:21:13.914-0400	[........................] udacity.atlanta	72.8 MB/3.2 GB (2.2%)
    2015-08-19T09:21:16.915-0400	[........................] udacity.atlanta	108.0 MB/3.2 GB (3.3%)
    2015-08-19T09:21:19.914-0400	[........................] udacity.atlanta	119.2 MB/3.2 GB (3.6%)
    2015-08-19T09:21:22.917-0400	[#.......................] udacity.atlanta	141.1 MB/3.2 GB (4.3%)
    2015-08-19T09:21:25.915-0400	[#.......................] udacity.atlanta	177.5 MB/3.2 GB (5.4%)
    2015-08-19T09:21:28.917-0400	[#.......................] udacity.atlanta	212.0 MB/3.2 GB (6.4%)
    2015-08-19T09:21:31.914-0400	[#.......................] udacity.atlanta	246.3 MB/3.2 GB (7.5%)
    2015-08-19T09:21:34.915-0400	[##......................] udacity.atlanta	282.6 MB/3.2 GB (8.6%)
    2015-08-19T09:21:37.915-0400	[##......................] udacity.atlanta	317.6 MB/3.2 GB (9.6%)
    2015-08-19T09:21:40.915-0400	[##......................] udacity.atlanta	350.0 MB/3.2 GB (10.6%)
    2015-08-19T09:21:43.914-0400	[##......................] udacity.atlanta	384.4 MB/3.2 GB (11.6%)
    2015-08-19T09:21:46.918-0400	[###.....................] udacity.atlanta	418.8 MB/3.2 GB (12.7%)
    2015-08-19T09:21:49.914-0400	[###.....................] udacity.atlanta	453.9 MB/3.2 GB (13.7%)
    2015-08-19T09:21:52.917-0400	[###.....................] udacity.atlanta	489.5 MB/3.2 GB (14.8%)
    2015-08-19T09:21:55.915-0400	[###.....................] udacity.atlanta	526.4 MB/3.2 GB (15.9%)
    2015-08-19T09:21:58.918-0400	[####....................] udacity.atlanta	557.5 MB/3.2 GB (16.9%)
    2015-08-19T09:22:01.914-0400	[####....................] udacity.atlanta	589.6 MB/3.2 GB (17.8%)
    2015-08-19T09:22:04.917-0400	[####....................] udacity.atlanta	620.5 MB/3.2 GB (18.8%)
    2015-08-19T09:22:07.914-0400	[####....................] udacity.atlanta	653.0 MB/3.2 GB (19.8%)
    2015-08-19T09:22:10.914-0400	[####....................] udacity.atlanta	681.5 MB/3.2 GB (20.6%)
    2015-08-19T09:22:13.916-0400	[#####...................] udacity.atlanta	711.5 MB/3.2 GB (21.5%)
    2015-08-19T09:22:16.921-0400	[#####...................] udacity.atlanta	744.5 MB/3.2 GB (22.5%)
    2015-08-19T09:22:19.914-0400	[#####...................] udacity.atlanta	775.5 MB/3.2 GB (23.5%)
    2015-08-19T09:22:22.914-0400	[#####...................] udacity.atlanta	806.6 MB/3.2 GB (24.4%)
    2015-08-19T09:22:25.916-0400	[######..................] udacity.atlanta	837.5 MB/3.2 GB (25.4%)
    2015-08-19T09:22:28.916-0400	[######..................] udacity.atlanta	869.0 MB/3.2 GB (26.3%)
    2015-08-19T09:22:31.914-0400	[######..................] udacity.atlanta	901.1 MB/3.2 GB (27.3%)
    2015-08-19T09:22:34.916-0400	[######..................] udacity.atlanta	931.8 MB/3.2 GB (28.2%)
    2015-08-19T09:22:37.915-0400	[######..................] udacity.atlanta	961.7 MB/3.2 GB (29.1%)
    2015-08-19T09:22:40.915-0400	[#######.................] udacity.atlanta	993.8 MB/3.2 GB (30.1%)
    2015-08-19T09:22:43.914-0400	[#######.................] udacity.atlanta	1.0 GB/3.2 GB (31.0%)
    2015-08-19T09:22:46.917-0400	[#######.................] udacity.atlanta	1.0 GB/3.2 GB (31.5%)
    2015-08-19T09:22:49.914-0400	[#######.................] udacity.atlanta	1.0 GB/3.2 GB (32.4%)
    2015-08-19T09:22:52.914-0400	[########................] udacity.atlanta	1.1 GB/3.2 GB (33.4%)
    2015-08-19T09:22:55.915-0400	[########................] udacity.atlanta	1.1 GB/3.2 GB (34.3%)
    2015-08-19T09:22:58.914-0400	[########................] udacity.atlanta	1.1 GB/3.2 GB (35.2%)
    2015-08-19T09:23:01.916-0400	[########................] udacity.atlanta	1.2 GB/3.2 GB (36.3%)
    2015-08-19T09:23:04.914-0400	[########................] udacity.atlanta	1.2 GB/3.2 GB (37.3%)
    2015-08-19T09:23:07.917-0400	[#########...............] udacity.atlanta	1.2 GB/3.2 GB (38.2%)
    2015-08-19T09:23:10.914-0400	[#########...............] udacity.atlanta	1.3 GB/3.2 GB (39.1%)
    2015-08-19T09:23:13.914-0400	[#########...............] udacity.atlanta	1.3 GB/3.2 GB (39.2%)
    2015-08-19T09:23:16.915-0400	[#########...............] udacity.atlanta	1.3 GB/3.2 GB (40.1%)
    2015-08-19T09:23:19.914-0400	[#########...............] udacity.atlanta	1.3 GB/3.2 GB (41.0%)
    2015-08-19T09:23:22.919-0400	[##########..............] udacity.atlanta	1.4 GB/3.2 GB (41.9%)
    2015-08-19T09:23:25.916-0400	[##########..............] udacity.atlanta	1.4 GB/3.2 GB (42.9%)
    2015-08-19T09:23:28.914-0400	[##########..............] udacity.atlanta	1.4 GB/3.2 GB (43.9%)
    2015-08-19T09:23:31.915-0400	[##########..............] udacity.atlanta	1.4 GB/3.2 GB (44.8%)
    2015-08-19T09:23:34.915-0400	[##########..............] udacity.atlanta	1.5 GB/3.2 GB (45.8%)
    2015-08-19T09:23:37.916-0400	[###########.............] udacity.atlanta	1.5 GB/3.2 GB (46.7%)
    2015-08-19T09:23:40.914-0400	[###########.............] udacity.atlanta	1.5 GB/3.2 GB (47.7%)
    2015-08-19T09:23:43.918-0400	[###########.............] udacity.atlanta	1.6 GB/3.2 GB (48.6%)
    2015-08-19T09:23:46.916-0400	[###########.............] udacity.atlanta	1.6 GB/3.2 GB (49.5%)
    2015-08-19T09:23:49.919-0400	[############............] udacity.atlanta	1.6 GB/3.2 GB (50.3%)
    2015-08-19T09:23:52.915-0400	[############............] udacity.atlanta	1.7 GB/3.2 GB (51.4%)
    2015-08-19T09:23:55.916-0400	[############............] udacity.atlanta	1.7 GB/3.2 GB (52.3%)
    2015-08-19T09:23:58.915-0400	[############............] udacity.atlanta	1.7 GB/3.2 GB (53.3%)
    2015-08-19T09:24:01.914-0400	[#############...........] udacity.atlanta	1.8 GB/3.2 GB (54.3%)
    2015-08-19T09:24:04.914-0400	[#############...........] udacity.atlanta	1.8 GB/3.2 GB (55.2%)
    2015-08-19T09:24:07.917-0400	[#############...........] udacity.atlanta	1.8 GB/3.2 GB (56.0%)
    2015-08-19T09:24:10.919-0400	[#############...........] udacity.atlanta	1.8 GB/3.2 GB (56.9%)
    2015-08-19T09:24:13.914-0400	[#############...........] udacity.atlanta	1.9 GB/3.2 GB (57.9%)
    2015-08-19T09:24:16.916-0400	[##############..........] udacity.atlanta	1.9 GB/3.2 GB (58.9%)
    2015-08-19T09:24:19.914-0400	[##############..........] udacity.atlanta	1.9 GB/3.2 GB (59.8%)
    2015-08-19T09:24:22.915-0400	[##############..........] udacity.atlanta	2.0 GB/3.2 GB (60.7%)
    2015-08-19T09:24:25.915-0400	[##############..........] udacity.atlanta	2.0 GB/3.2 GB (61.6%)
    2015-08-19T09:24:28.915-0400	[##############..........] udacity.atlanta	2.0 GB/3.2 GB (62.5%)
    2015-08-19T09:24:31.914-0400	[###############.........] udacity.atlanta	2.0 GB/3.2 GB (63.5%)
    2015-08-19T09:24:34.915-0400	[###############.........] udacity.atlanta	2.1 GB/3.2 GB (64.3%)
    2015-08-19T09:24:37.914-0400	[###############.........] udacity.atlanta	2.1 GB/3.2 GB (65.3%)
    2015-08-19T09:24:40.915-0400	[###############.........] udacity.atlanta	2.1 GB/3.2 GB (66.2%)
    2015-08-19T09:24:43.914-0400	[################........] udacity.atlanta	2.2 GB/3.2 GB (67.3%)
    2015-08-19T09:24:46.916-0400	[################........] udacity.atlanta	2.2 GB/3.2 GB (67.8%)
    2015-08-19T09:24:49.914-0400	[################........] udacity.atlanta	2.2 GB/3.2 GB (68.8%)
    2015-08-19T09:24:52.915-0400	[################........] udacity.atlanta	2.2 GB/3.2 GB (69.6%)
    2015-08-19T09:24:55.914-0400	[################........] udacity.atlanta	2.3 GB/3.2 GB (70.6%)
    2015-08-19T09:24:58.916-0400	[#################.......] udacity.atlanta	2.3 GB/3.2 GB (71.6%)
    2015-08-19T09:25:01.914-0400	[#################.......] udacity.atlanta	2.3 GB/3.2 GB (72.6%)
    2015-08-19T09:25:04.914-0400	[#################.......] udacity.atlanta	2.4 GB/3.2 GB (73.6%)
    2015-08-19T09:25:07.917-0400	[#################.......] udacity.atlanta	2.4 GB/3.2 GB (74.5%)
    2015-08-19T09:25:10.917-0400	[##################......] udacity.atlanta	2.4 GB/3.2 GB (75.5%)
    2015-08-19T09:25:13.920-0400	[##################......] udacity.atlanta	2.5 GB/3.2 GB (76.4%)
    2015-08-19T09:25:16.914-0400	[##################......] udacity.atlanta	2.5 GB/3.2 GB (77.2%)
    2015-08-19T09:25:19.914-0400	[##################......] udacity.atlanta	2.5 GB/3.2 GB (78.2%)
    2015-08-19T09:25:22.914-0400	[##################......] udacity.atlanta	2.6 GB/3.2 GB (79.1%)
    2015-08-19T09:25:25.914-0400	[###################.....] udacity.atlanta	2.6 GB/3.2 GB (80.0%)
    2015-08-19T09:25:28.915-0400	[###################.....] udacity.atlanta	2.6 GB/3.2 GB (80.9%)
    2015-08-19T09:25:31.914-0400	[###################.....] udacity.atlanta	2.6 GB/3.2 GB (81.9%)
    2015-08-19T09:25:34.916-0400	[###################.....] udacity.atlanta	2.7 GB/3.2 GB (82.8%)
    2015-08-19T09:25:37.919-0400	[####################....] udacity.atlanta	2.7 GB/3.2 GB (83.8%)
    2015-08-19T09:25:40.918-0400	[####################....] udacity.atlanta	2.7 GB/3.2 GB (84.4%)
    2015-08-19T09:25:43.914-0400	[####################....] udacity.atlanta	2.8 GB/3.2 GB (85.3%)
    2015-08-19T09:25:46.916-0400	[####################....] udacity.atlanta	2.8 GB/3.2 GB (86.0%)
    2015-08-19T09:25:49.916-0400	[####################....] udacity.atlanta	2.8 GB/3.2 GB (87.1%)
    2015-08-19T09:25:52.914-0400	[#####################...] udacity.atlanta	2.8 GB/3.2 GB (88.3%)
    2015-08-19T09:25:55.918-0400	[#####################...] udacity.atlanta	2.9 GB/3.2 GB (89.7%)
    2015-08-19T09:25:58.914-0400	[#####################...] udacity.atlanta	2.9 GB/3.2 GB (90.9%)
    2015-08-19T09:26:01.916-0400	[#####################...] udacity.atlanta	2.9 GB/3.2 GB (90.9%)
    2015-08-19T09:26:04.916-0400	[######################..] udacity.atlanta	3.0 GB/3.2 GB (92.2%)
    2015-08-19T09:26:07.915-0400	[######################..] udacity.atlanta	3.0 GB/3.2 GB (93.4%)
    2015-08-19T09:26:10.914-0400	[######################..] udacity.atlanta	3.1 GB/3.2 GB (94.7%)
    2015-08-19T09:26:13.914-0400	[######################..] udacity.atlanta	3.1 GB/3.2 GB (95.8%)
    2015-08-19T09:26:16.918-0400	[#######################.] udacity.atlanta	3.1 GB/3.2 GB (97.0%)
    2015-08-19T09:26:19.917-0400	[#######################.] udacity.atlanta	3.2 GB/3.2 GB (98.1%)
    2015-08-19T09:26:22.914-0400	[#######################.] udacity.atlanta	3.2 GB/3.2 GB (99.3%)
    2015-08-19T09:26:24.543-0400	imported 12052377 documents



    from pymongo import MongoClient
    client = MongoClient("mongodb://localhost:27017")
    db = client.udacity


    import pprint
    s= db.atlanta.find_one() 
    pprint.pprint(s)

    {u'_id': ObjectId('55d1e6610a269d5d25071e85'),
     u'created': {u'changeset': u'3173372',
                  u'timestamp': u'2009-11-21T04:30:48Z',
                  u'uid': u'147510',
                  u'user': u'woodpeck_fixbot',
                  u'version': u'2'},
     u'id': u'52374108',
     u'pos': [32.87149, -85.193652],
     u'type': u'node'}


####Running Mongodb Queries

#####Document Size


    db.atlanta.find().count()




    12052377




    db.atlanta.find({"type":"node"}).count()




    11287611




    db.atlanta.find({"type":"way"}).count()




    764629




    len(db.atlanta.distinct("created.user"))




    1646




    db.atlanta.distinct("created.user")[:10]




    [u'woodpeck_fixbot',
     u'greenv505',
     u'DKNOTT',
     u'xybot',
     u'NE2',
     u'oldtopos',
     u'ramyaragupathy',
     u'maven149',
     u'Rub21',
     u'Pnrrth']




    #Top two contributors
    cursor = db.atlanta.aggregate([{"$group":{"_id":"$created.user", "count":{"$sum":1}}}, {"$sort":{"count":-1}}, {"$limit":10}])
    list(cursor)




    [{u'_id': u'Liber', u'count': 11199566},
     {u'_id': u'Saikrishna_FultonCountyImport', u'count': 4528820},
     {u'_id': u'woodpeck_fixbot', u'count': 3143098},
     {u'_id': u'rjhale1971', u'count': 562512},
     {u'_id': u'Jack Kittle Buildings', u'count': 494618},
     {u'_id': u'Jack the Ripper', u'count': 463286},
     {u'_id': u'maven149', u'count': 310980},
     {u'_id': u'Chris Lawrence', u'count': 250380},
     {u'_id': u'macon_cfa', u'count': 200222},
     {u'_id': u'Rub21', u'count': 171496}]




    cursor = db.atlanta.aggregate([{"$group":{"_id":"$created.user", "count":{"$sum":1}}}, {"$sort":{"count":-1}}])
    top_10 = list(cursor)[:10]


    #Sum of counts by top 10 contributors
    total_count = []
    for i in top_10:
        total_count.append(i['count'])
    sum(total_count)




    10662489




    #Number of contributors who only contributed once or twice
    cursor = db.atlanta.aggregate([
    	{"$group":{"_id":"$created.user", "count":{"$sum":1}}}, 
    	{"$group":{"_id":"$count", "num_users":{"$sum":1}}}, 
    	{"$sort":{"_id":1}}, 
    			  ])
    list(cursor)[:2]




    [{u'_id': 1, u'num_users': 276}, {u'_id': 2, u'num_users': 134}]




    #Top 5 most common amenity type
    cursor = db.atlanta.aggregate([
    	{"$match":{"amenity":{"$exists":1}}}, 
    	{"$group":{"_id":"$amenity","count":{"$sum":1}}}, 
    	{"$sort":{"count":-1}},
    	])
    list(cursor)[:5]




    [{u'_id': u'place_of_worship', u'count': 5630},
     {u'_id': u'parking', u'count': 3082},
     {u'_id': u'school', u'count': 2355},
     {u'_id': u'grave_yard', u'count': 2180},
     {u'_id': u'restaurant', u'count': 1123}]




    #Top 5 most common cuisine at restaurants
    cursor =  db.atlanta.aggregate([
     	{"$match":{"amenity":{"$exists":1}, "amenity":"restaurant"}}, 
     	{"$group":{"_id":"$cuisine", "count":{"$sum":1}}},        
     	{"$sort":{"count":-1}}, 
        {"$limit":5}
     	])
    list(cursor)




    [{u'_id': None, u'count': 398},
     {u'_id': u'american', u'count': 97},
     {u'_id': u'pizza', u'count': 83},
     {u'_id': u'mexican', u'count': 69},
     {u'_id': u'burger', u'count': 31}]




    #Top 4 most common religion at place of worship
    cursor = db.atlanta.aggregate([{"$match":{"amenity":{"$exists":1}, "amenity":"place_of_worship"}},
                        {"$group":{"_id":"$religion", "count":{"$sum":1}}},
                        {"$sort":{"count":-1}}, 
                        {"$limit":4}
                        ])
    list(cursor)




    [{u'_id': u'christian', u'count': 4293},
     {u'_id': None, u'count': 1314},
     {u'_id': u'jewish', u'count': 9},
     {u'_id': u'muslim', u'count': 5}]




    #Top 5 amenity type that have wifi
    cursor = db.atlanta.aggregate([{"$match":{"wifi":{"$exists":1}, "amenity":{"$exists":1}}},
                        {"$group":{"_id":"$amenity", "count":{"$sum":1}}},
                        {"$sort":{"count":-1}}, 
                        {"$limit":5}
                        ])
    list(cursor)




    [{u'_id': u'cafe', u'count': 5},
     {u'_id': u'restaurant', u'count': 4},
     {u'_id': u'pub', u'count': 2},
     {u'_id': u'bar', u'count': 1}]




    #Payment types and count if exists
    cursor = db.atlanta.aggregate([{"$match":{"payment":{"$exists":1}}},
                        {"$group":{"_id":"$payment", "count":{"$sum":1}}},
                        {"$sort":{"count":-1}}, 
                        {"$limit":5}
                        ])
    list(cursor)




    [{u'_id': {u'bitcoin': u'yes'}, u'count': 27},
     {u'_id': {u'bitcoin': u'yes', u'litecoin': u'yes'}, u'count': 3},
     {u'_id': {u'coins': u'yes'}, u'count': 1},
     {u'_id': u'cash', u'count': 1}]




    # Amenity types that accept bitcoin
    cursor = db.atlanta.aggregate([{"$match":{"payment.bitcoin":{"$eq":"yes"}}},
                        {"$group":{"_id":"$amenity", "count":{"$sum":1}}},
                        {"$sort":{"count":-1}}, 
                        {"$limit":5}
                        ])
    list(cursor)




    [{u'_id': None, u'count': 20},
     {u'_id': u'restaurant', u'count': 7},
     {u'_id': u'bar', u'count': 1},
     {u'_id': u'atm', u'count': 1},
     {u'_id': u'doctors', u'count': 1}]




    # Top 5 Users who contributed bitcoin info
    cursor = db.atlanta.aggregate([{"$match":{"payment.bitcoin":{"$eq":"yes"}}},
                        {"$group":{"_id":"$created.user", "count":{"$sum":1}}},
                        {"$sort":{"count":-1}}, 
                        {"$limit":5}
                        ])
    list(cursor)




    [{u'_id': u'Blobo123', u'count': 16},
     {u'_id': u'oldenburg69', u'count': 12},
     {u'_id': u'dreyzehner', u'count': 4},
     {u'_id': u'Robert Wilson', u'count': 4},
     {u'_id': u"Gino's Classic Barber Shoppe", u'count': 2}]




    # Top 5 amenity types that Saikrishna_FultonCountyImport contributed
    cursor = db.atlanta.aggregate([{"$match":{"created.user":{"$eq":"Saikrishna_FultonCountyImport"}}},
                        {"$group":{"_id":"$amenity", "count":{"$sum":1}}},
                        {"$sort":{"count":-1}}, 
                        {"$limit":5}
                        ])
    list(cursor)





    [{u'_id': None, u'count': 4525766},
     {u'_id': u'place_of_worship', u'count': 2558},
     {u'_id': u'hospital', u'count': 196},
     {u'_id': u'parking', u'count': 126},
     {u'_id': u'social_centre', u'count': 48}]




    # Top 5 amenity types that Liber contributed
    cursor = db.atlanta.aggregate([{"$match":{"created.user":{"$eq":"Liber"}}},
                        {"$group":{"_id":"$amenity", "count":{"$sum":1}}},
                        {"$sort":{"count":-1}}, 
                        {"$limit":5}
                        ])
    list(cursor)




    [{u'_id': None, u'count': 11199554},
     {u'_id': u'parking', u'count': 2},
     {u'_id': u'place_of_worship', u'count': 2},
     {u'_id': u'police', u'count': 2},
     {u'_id': u'courthouse', u'count': 2}]



###Lesson 6 Codes


    ## Iterative Parsing
    
    #!/usr/bin/env python
    # -*- coding: utf-8 -*-
    """
    Your task is to use the iterative parsing to process the map file and
    find out not only what tags are there, but also how many, to get the
    feeling on how much of which data you can expect to have in the map.
    Fill out the count_tags function. It should return a dictionary with the 
    tag name as the key and number of times this tag can be encountered in 
    the map as value.
    
    Note that your code will be tested with a different data file than the 'example.osm'
    """
    import xml.etree.cElementTree as ET
    import pprint
    import re
    
    def count_tags(filename):
        tree = ET.parse(filename)
        root = tree.getroot()
    
        dict_tag = {}
        tag_list = []
        
        tag_list.append(str(root).split("'")[1])
        for tag in root:
            tag_list.append(str(tag).split("'")[1])
            for tag_1 in tag:
                tag_list.append(str(tag_1).split("'")[1])
                for tag_2 in tag_1:
                    tag_list.append(str(tag_2).split("'")[1])
        
        key_list = list(set(tag_list))
        for key in key_list:
            dict_tag[key] = tag_list.count(key)
        print dict_tag
        return dict_tag
         
    def test():
    
        tags = count_tags('example.osm')
        pprint.pprint(tags)
        assert tags == {'bounds': 1,
                         'member': 3,
                         'nd': 4,
                         'node': 20,
                         'osm': 1,
                         'relation': 1,
                         'tag': 7,
                         'way': 1}
    
        
    
    if __name__ == "__main__":
        test()


    ---------------------------------------------------------------------------
    IOError                                   Traceback (most recent call last)

    <ipython-input-14-19ba7c56007d> in <module>()
         54 
         55 if __name__ == "__main__":
    ---> 56     test()
    

    <ipython-input-14-19ba7c56007d> in test()
         40 def test():
         41 
    ---> 42     tags = count_tags('example.osm')
         43     pprint.pprint(tags)
         44     assert tags == {'bounds': 1,


    <ipython-input-14-19ba7c56007d> in count_tags(filename)
         18 
         19 def count_tags(filename):
    ---> 20     tree = ET.parse(filename)
         21     root = tree.getroot()
         22 


    <string> in parse(source, parser)


    <string> in parse(self, source, parser)


    IOError: [Errno 2] No such file or directory: 'example.osm'



    #Tag Types
    
    #!/usr/bin/env python
    # -*- coding: utf-8 -*-
    import xml.etree.cElementTree as ET
    import pprint
    import re
    from collections import OrderedDict
    """
    Your task is to explore the data a bit more.
    Before you process the data and add it into MongoDB, you should
    check the "k" value for each "<tag>" and see if they can be valid keys in MongoDB,
    as well as see if there are any other potential problems.
    
    We have provided you with 3 regular expressions to check for certain patterns
    in the tags. As we saw in the quiz earlier, we would like to change the data model
    and expand the "addr:street" type of keys to a dictionary like this:
    {"address": {"street": "Some value"}}
    So, we have to see if we have such tags, and if we have any tags with problematic characters.
    Please complete the function 'key_type'.
    """
    
    
    lower = re.compile(r'^([a-z]|_)*$')
    lower_colon = re.compile(r'^([a-z]|_)*:([a-z]|_)*$')
    problemchars = re.compile(r'[=\+/&<>;\'"\?%#$@\,\. \t\r\n]')
    
    
    
    def key_type(element, keys):
        if element.tag == "tag":
            k_val = element.get("k")
            if bool(lower.search(k_val)):
                keys["lower"] += 1
            elif bool(lower_colon.search(k_val)): 
                keys["lower_colon"] += 1
            elif bool(problemchars.search(k_val)):
                keys["problemchars"] += 1
            else:
                keys["other"] += 1
        
        return keys
    
    def process_map(filename):
        keys = {"lower": 0, "lower_colon": 0, "problemchars": 0, "other": 0}
        for _, element in ET.iterparse(filename):
            keys = key_type(element, keys)
        print "my solution: ", keys
        return keys
    
    
    
    def test():
        # You can use another testfile 'map.osm' to look at your solution
        # Note that the assertions will be incorrect then.
        keys = process_map('example.osm')
        pprint.pprint(keys)
        assert keys == {'lower': 5, 'lower_colon': 0, 'other': 1, 'problemchars': 1}
    
    
    if __name__ == "__main__":
        test()


    #Exploring Users
    
    #!/usr/bin/env python
    # -*- coding: utf-8 -*-
    import xml.etree.cElementTree as ET
    import pprint
    import re
    """
    Your task is to explore the data a bit more.
    The first task is a fun one - find out how many unique users
    have contributed to the map in this particular area!
    
    The function process_map should return a set of unique user IDs ("uid")
    """
    
    def get_user(element):
        return
    
    
    def process_map(filename):
        users = set()
        u = []
        for _, element in ET.iterparse(filename):
            if element.get("uid"):
                k_val = element.get("uid")
                u.append(k_val)
        users = set(u)
        return users
    
    
    def test():
    
        users = process_map('example.osm')
        pprint.pprint(users)
        assert len(users) == 6
    
    
    
    if __name__ == "__main__":
        test()


    #Improving Street Names
    """
    Your task in this exercise has two steps:
    
    - audit the OSMFILE and change the variable 'mapping' to reflect the changes needed to fix 
        the unexpected street types to the appropriate ones in the expected list.
        You have to add mappings only for the actual problems you find in this OSMFILE,
        not a generalized solution, since that may and will depend on the particular area you are auditing.
    - write the update_name function, to actually fix the street name.
        The function takes a string with street name as an argument and should return the fixed name
        We have provided a simple test so that you see what exactly is expected
    """
    import xml.etree.cElementTree as ET
    from collections import defaultdict
    import re
    import pprint
    import string as str
    
    OSMFILE = "example.osm"
    street_type_re = re.compile(r'\b\S+\.?$', re.IGNORECASE)
    
    
    expected = ["Street", "Avenue", "Boulevard", "Drive", "Court", "Place", "Square", "Lane", "Road", 
                "Trail", "Parkway", "Commons"]
    
    # UPDATE THIS VARIABLE
    mapping = { "St": "Street",
                "St.": "Street",
                "Rd.":"Road",
                "Ave" : "Avenue"
                }
    
    #print "old mapping dictionary: ", mapping
    def audit_street_type(street_types, street_name):
        m = street_type_re.search(street_name)
        if m:
            street_type = m.group()
            if street_type not in expected:
                street_types[street_type].add(street_name)
    
    
    def is_street_name(elem):
        return (elem.attrib['k'] == "addr:street")
    
    
    def audit(osmfile):
        osm_file = open(osmfile, "r")
        street_types = defaultdict(set)
        for event, elem in ET.iterparse(osm_file, events=("start",)):
    
            if elem.tag == "node" or elem.tag == "way":
                for tag in elem.iter("tag"):
                    if is_street_name(tag):
                        audit_street_type(street_types, tag.attrib['v'])
    
        return street_types
    
    
    def update_name(name, mapping):
        new_name = name.split(" ")
        if new_name[-1] in mapping.keys():
            new_name[-1] = mapping[new_name[-1]]
        name = " ".join(new_name)
        return name
        """
        bad_word = street_type_re.findall(name)[0]
        #This will update the mapping dictionary with the bad word and replacement word from expected
        for word in expected:
            if (word.startswith(bad_word)) or all((letter in word) for letter in bad_word.strip(".")):
                mapping[bad_word] = word
        #This replaces name
        if bad_word:
            name = name.replace(bad_word, mapping[bad_word])
        return name
        """
    
    def test():
        st_types = audit(OSMFILE)
        assert len(st_types) == 3
        pprint.pprint(dict(st_types))
    
        for st_type, ways in st_types.iteritems():
            for name in ways:
                better_name = update_name(name, mapping)
                print name, "=>", better_name
                if name == "West Lexington St.":
                    assert better_name == "West Lexington Street"
                if name == "Baldwin Rd.":
                    assert better_name == "Baldwin Road"
        #print "new mapping dictionary: ", mapping
    
    if __name__ == '__main__':
        test()
        print mapping


    #Preparing for Database
    #!/usr/bin/env python
    # -*- coding: utf-8 -*-
    import xml.etree.cElementTree as ET
    import pprint
    import re
    import codecs
    import json
    """
    Your task is to wrangle the data and transform the shape of the data
    into the model we mentioned earlier. The output should be a list of dictionaries
    that look like this:
    
    {
    "id": "2406124091",
    "type: "node",
    "visible":"true",
    "created": {
              "version":"2",
              "changeset":"17206049",
              "timestamp":"2013-08-03T16:43:42Z",
              "user":"linuxUser16",
              "uid":"1219059"
            },
    "pos": [41.9757030, -87.6921867],
    "address": {
              "housenumber": "5157",
              "postcode": "60625",
              "street": "North Lincoln Ave"
            },
    "amenity": "restaurant",
    "cuisine": "mexican",
    "name": "La Cabana De Don Luis",
    "phone": "1 (773)-271-5176"
    }
    
    You have to complete the function 'shape_element'.
    We have provided a function that will parse the map file, and call the function with the element
    as an argument. You should return a dictionary, containing the shaped data for that element.
    We have also provided a way to save the data in a file, so that you could use
    mongoimport later on to import the shaped data into MongoDB. 
    
    Note that in this exercise we do not use the 'update street name' procedures
    you worked on in the previous exercise. If you are using this code in your final
    project, you are strongly encouraged to use the code from previous exercise to 
    update the street names before you save them to JSON. 
    
    In particular the following things should be done:
    - you should process only 2 types of top level tags: "node" and "way"
    - all attributes of "node" and "way" should be turned into regular key/value pairs, except:
        - attributes in the CREATED array should be added under a key "created"
        - attributes for latitude and longitude should be added to a "pos" array,
          for use in geospacial indexing. Make sure the values inside "pos" array are floats
          and not strings. 
    - if second level tag "k" value contains problematic characters, it should be ignored
    - if second level tag "k" value starts with "addr:", it should be added to a dictionary "address"
    - if second level tag "k" value does not start with "addr:", but contains ":", you can process it
      same as any other tag.
    - if there is a second ":" that separates the type/direction of a street,
      the tag should be ignored, for example:
    
    <tag k="addr:housenumber" v="5158"/>
    <tag k="addr:street" v="North Lincoln Avenue"/>
    <tag k="addr:street:name" v="Lincoln"/>
    <tag k="addr:street:prefix" v="North"/>
    <tag k="addr:street:type" v="Avenue"/>
    <tag k="amenity" v="pharmacy"/>
    
      should be turned into:
    
    {...
    "address": {
        "housenumber": 5158,
        "street": "North Lincoln Avenue"
    }
    "amenity": "pharmacy",
    ...
    }
    
    - for "way" specifically:
    
      <nd ref="305896090"/>
      <nd ref="1719825889"/>
    
    should be turned into
    "node_refs": ["305896090", "1719825889"]
    """
    
    
    lower = re.compile(r'^([a-z]|_)*$')
    lower_colon = re.compile(r'^([a-z]|_)*:([a-z]|_)*$')
    problemchars = re.compile(r'[=\+/&<>;\'"\?%#$@\,\. \t\r\n]')
    
    CREATED = [ "version", "changeset", "timestamp", "user", "uid"]
    
    
    def shape_element(element):
        node = {}
        pos = ["lat", "lon"]
    
        if element.tag == "node" or element.tag == "way" :
            node["created"] = {}
            node["address"] = {}
            node["pos"] = []
            node["node_refs"] = []
            node["type"] = element.tag
            for key in element.attrib.keys():
                if key in CREATED:
                    node["created"][key] = element.attrib[key]
                elif key in pos:
                    if key == "lat":
                        if len(node["pos"]) == 0:
                            node["pos"].append(float(element.attrib[key]))
                        else:
                            node["pos"][0] = float(element.attrib[key])
                    else:
                        if len(node["pos"]) == 0:
                            node["pos"].append("")
                        node["pos"].append(float(element.attrib[key]))
                        #node["pos"].append(float(element.attrib[key]))
                else:
                    node[key] = element.attrib[key]
            # if k and v are available, add them to dict
            for i in element:
                if (i.tag == "tag") and (bool(problemchars.search(i.attrib.get('k')))==False):
                    if (i.attrib.get('k').startswith("addr")) and (i.attrib.get('k').count(":")==1):
                        node["address"][i.attrib.get('k').split(":")[1]] = i.attrib.get('v')
                    elif (i.attrib.get('k').startswith("address")) and (i.attrib.get('k').count(":") > 1):
                        pass
                    else:
                        node[i.attrib.get('k')] = i.attrib.get('v')
                elif (i.tag == "nd"):
                    node["node_refs"].append(i.attrib.get('ref'))    
                            
            #delete empty items
            items_delete = []
            for i in node:
                if not node[i]:
                    items_delete.append(i)
            for i in items_delete:
                del node[i]
    
            return node
        else:
            return None
    
    
    def process_map(file_in, pretty = False):
        # You do not need to change this file
        file_out = "{0}.json".format(file_in)
        data = []
        with codecs.open(file_out, "w") as fo:
            for _, element in ET.iterparse(file_in):
                el = shape_element(element)
                if el:
                    data.append(el)
                    if pretty:
                        fo.write(json.dumps(el, indent=2)+"\n")
                    else:
                        fo.write(json.dumps(el) + "\n")
        return data
    
    def test():
        # NOTE: if you are running this code on your computer, with a larger dataset, 
        # call the process_map procedure with pretty=False. The pretty=True option adds 
        # additional spaces to the output, making it significantly larger.
        data = process_map('example.osm', True)
        #pprint.pprint(data)
        
        correct_first_elem = {
            "id": "261114295", 
            "visible": "true", 
            "type": "node", 
            "pos": [41.9730791, -87.6866303], 
            "created": {
                "changeset": "11129782", 
                "user": "bbmiller", 
                "version": "7", 
                "uid": "451048", 
                "timestamp": "2012-03-28T18:31:23Z"
            }
        }
        assert data[0] == correct_first_elem
        assert data[-1]["address"] == {
                                        "street": "West Lexington St.", 
                                        "housenumber": "1412"
                                          }
        assert data[-1]["node_refs"] == [ "2199822281", "2199822390",  "2199822392", "2199822369", 
                                        "2199822370", "2199822284", "2199822281"]
    
    if __name__ == "__main__":
        test()
