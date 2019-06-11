Familiarization of TNG Data Acess
Goals

Graph 3D time-dependent distribution of things (e.g. stellar mass, Europeum, star formation rate)
import requests
import h5py
import numpy as np
import matplotlib.pyplot as plt
base_url = 'http://www.tng-project.org/api/'
headers = {'api-key': '869b608c817d0a731e5439be3345bdf7'}
def get(path, params=None):
    # make HTTP GET request to path
    r = requests.get(path, params=params, headers=headers)
​
    # raise exception if response code is not HTTP SUCCESS (200)
    r.raise_for_status()
​
    if r.headers['content-type'] == 'application/json':
        return r.json() # parse json responses automatically
​
    if 'content-disposition' in r.headers:
        filename = r.headers['content-disposition'].split("filename=")[1]
        with open(filename, 'wb') as f:
            f.write(r.content)
        return filename # return the filename string
​
    return r
r = get(base_url)
name_list = [sim['name'] for sim in r['simulations']]
index = name_list.index('Illustris-3')
sim = get(r['simulations'][index]['url'])
snaps_json = get(sim['snapshots'])
snap = get(snaps_json[0]['url'])
snap_file = get(snap['files']['snapshot'])
file = snap_file['files'][0]
​
with h5py.File(get(file), 'r') as f:
    f.keys()
​
cutout = get(file, {'gas':'ParticleIDs,Masses,StarFormationRate'})
​
with h5py.File(cutout,'r') as f:
    particle_ids = f['PartType0']['ParticleIDs'][:]
    dens = np.log10(f['PartType0']['Masses'][:])
    
len(particle_ids)
2936930
There are a lot of variables and sometimes it is hard to remember what each one of them mean, this link http://www.tng-project.org/data/docs/specifications/#sec1a gives a description of all of these variables and a basic structure of the API.

​

